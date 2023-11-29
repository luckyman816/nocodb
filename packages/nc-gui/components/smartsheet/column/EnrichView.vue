<script lang="ts" setup>
import type { ColumnReqType, ColumnType } from 'nocodb-sdk'
import { UITypes, isLinksOrLTAR, isVirtualCol } from 'nocodb-sdk'
import {
  IsFormInj,
  IsKanbanInj,
  MetaInj,
  ReloadViewDataHookInj,
  computed,
  inject,
  isJSON,
  isTextArea,
  message,
  onMounted,
  ref,
  uiTypes,
  useBase,
  useEnrichColumnCreateStoreOrThrow,
  useGlobal,
  useI18n,
  useMetas,
  useNuxtApp,
  watchEffect,
} from '#imports'
import MdiMinusIcon from '~icons/mdi/minus-circle-outline'
import MdiIdentifierIcon from '~icons/mdi/identifier'
const { formStatus, showEnrichModal, invitationUsersData, isInvitationLinkCopied } = storeToRefs(useShare())
const { resetData } = useShare()
watch(showEnrichModal, (val) => {
  if (!val) {
    setTimeout(() => {
      resetData()
    }, 500)
  }
})
const props = defineProps<{
  preload?: Partial<ColumnType>
  columnPosition?: Pick<ColumnReqType, 'column_order'>
  // Disable styles like border, shadow to be embedded on other components
  embedMode?: boolean
  // Will be used to show where ever text 'Column' is used.
  // i.e 'Column Name' label in form, thus will be of form `${columnLabel} Name`
  columnLabel?: string
  hideTitle?: boolean
  hideType?: boolean
  hideAdditionalOptions?: boolean
  fromTableExplorer?: boolean
}>()

const emit = defineEmits(['submit', 'cancel', 'mounted', 'add', 'update'])

const {  generateNewColumnMeta, addOrUpdateEnrich, onUidtOrIdTypeChange, onAlter, validateInfos } = useEnrichColumnCreateStoreOrThrow()

const { getMeta } = useMetas()

const { t } = useI18n()

const columnLabel = computed(() => props.columnLabel || t('objects.field'))

const { $e } = useNuxtApp()

const { appInfo } = useGlobal()

const { betaFeatureToggleState } = useBetaFeatureToggle()

const { openedViewsTab } = storeToRefs(useViewsStore())

const { predictColumnType: _predictColumnType } = useNocoEe()

const meta = inject(MetaInj, ref())

const isForm = inject(IsFormInj, ref(false))

const isKanban = inject(IsKanbanInj, ref(false))

const { isMysql, isMssql, isXcdbBase } = useBase()

const reloadDataTrigger = inject(ReloadViewDataHookInj)

const advancedOptions = ref(false)

const mounted = ref(false)

const columnToValidate = [UITypes.Email, UITypes.URL, UITypes.PhoneNumber]

const onlyNameUpdateOnEditColumns = [UITypes.LinkToAnotherRecord, UITypes.Lookup, UITypes.Rollup, UITypes.Links]

const geoDataToggleCondition = (t: { name: UITypes }) => {
  if (!appInfo.value.ee) return true

  return betaFeatureToggleState.show ? betaFeatureToggleState.show : !t.name.includes(UITypes.GeoData)
}

const showDeprecated = ref(false)

const uiTypesOptions = computed<typeof uiTypes>(() => {
  return [
    ...uiTypes
      .filter((t) => geoDataToggleCondition(t) && (!isEdit.value || !t.virtual) && (!t.deprecated || showDeprecated.value))
      .filter((t) => !(t.name === UITypes.SpecificDBType && isXcdbBase(meta.value?.source_id))),
    ...(!isEdit.value && meta?.value?.columns?.every((c) => !c.pk)
      ? [
          {
            name: UITypes.ID,
            icon: MdiIdentifierIcon,
            virtual: 0,
          },
        ]
      : []),
  ]
})

const reloadMetaAndData = async () => {
  await getMeta(meta.value?.id as string, true)

  if (!isKanban.value) {
    reloadDataTrigger?.trigger()
  }
}

const saving = ref(false)

async function onSubmit1() {
  console.log("asdfasdfasdf")
  saving.value = true
  const saved = await addOrUpdateEnrich(reloadMetaAndData, props.columnPosition)
  saving.value = false

  // if (!saved) return

  // // add delay to complete minimize transition
  // setTimeout(() => {
  //   advancedOptions.value = false
  // }, 500)
  // emit('submit')

  if (isForm.value) {
    $e('a:form-view:add-new-field')
  }
}

// focus and select the column name field
const antInput = ref()
watchEffect(() => {
  if (antInput.value && formState.value) {
    // todo: replace setTimeout
    setTimeout(() => {
      // focus and select input only if active element is not an input or textarea
      if (document.activeElement === document.body || document.activeElement === null) {
        antInput.value?.focus()
        antInput.value?.select()
      }
    }, 300)
  }
  advancedOptions.value = false
})

onMounted(() => {
  generateNewColumnMeta()
})

const handleEscape = (event: KeyboardEvent): void => {
  if (event.key === 'Escape') emit('cancel')
}

const isFieldsTab = computed(() => {
  return openedViewsTab.value === 'field'
})

if (props.fromTableExplorer) {
  watch(
    formState,
    () => {
      if (mounted.value) emit('update', formState.value)
    },
    { deep: true },
  )
}
</script>

<template>
  <a-modal
    v-model:visible="showEnrichModal"
    class="!top-[1%]"
    :class="{ active: showEnrichModal }"
    wrap-class-name="nc-modal-share-collaborate"
    :closable="false"
    :mask-closable="formStatus === 'base-collaborateSaving' ? false : true"
    :ok-button-props="{ hidden: true } as any"
    :cancel-button-props="{ hidden: true } as any"
    :footer="null"
    :width="formStatus === 'manageCollaborators' ? '80rem' : '60rem'"
  >
    <div style="display: flex">
      <ul>
        <li>company</li>
        <li><div>Company Data</div></li>
        <li><div>Domains</div></li>
        <li><div>Job Listenings</div></li>
        <li><div>Tech Stack</div></li>
      </ul>
      <ui v-if="true" class="ul-container">
        <li><button class="div-container" @click="onSubmit1()">Employee Count</button></li>
        <li><div class="div-container">Revenue</div></li>
        <li><div class="div-container">Description</div></li>
        <li><div class="div-container">Industry</div></li>
        <li><div class="div-container">Founded Data</div></li>
        <li><div class="div-container">Country</div></li>
        <li><div class="div-container">Address</div></li>
      </ui>
    </div>
  </a-modal>
</template>

<style lang="scss" scoped>
.share-collapse-item {
  @apply !rounded-lg !mb-2 !mt-4 !border-0;
}

.ant-collapse {
  @apply !bg-white !border-0;
}
</style>

<style lang="scss">
.nc-modal-share-collaborate {
  .ant-modal {
    top: 10vh !important;
  }

  .share-view,
  .share-base {
    @apply !border-1 border-gray-200 mx-3 rounded-lg mt-3 px-1 py-1;
  }

  .ant-collapse-item {
    @apply !border-1 border-gray-100;
  }

  .ant-collapse-content {
    @apply !border-t-0;
  }

  .ant-collapse-content-box {
    @apply !p-0;
  }

  .ant-modal-content {
    @apply !rounded-lg !px-1 !py-2;
  }

  .ant-select-selector {
    @apply !rounded-md !border-gray-200 !border-1;
  }

  .ant-form-item {
    @apply !my-0;
  }

  .ant-form-item-explain {
    @apply !ml-3;
  }

  .ant-select {
    @apply !p-0.5;
  }

  .ant-select-selector {
    @apply !bg-white;
  }
  ul {
    list-style-type: none;
    margin: 5vw, 5vw, 5vw, 5vw;
    padding-left: 10px;
    padding-right: 10px;
    width: 10vw;
    border-right: solid 1px;
    border-color: grey;
    background-color: white;
  }

  li div {
    display: block;
    color: #000;
    padding: 8px 16px;
    font-size: 18px;
    text-decoration: none;
  }
  .ul-container {
    list-style-type: none;
    margin: 5vw, 5vw, 5vw, 5vw;
    padding-left: 10px;
    padding-right: 10px;
    width: 30vw;
    background-color: white;
  }
  .div-container {
    display: block;
    color: #000;
    padding: 8px 16px;
    font-size: 18px;
    text-decoration: none;
  }
  .div-container:hover {
    background-color: lightgrey;
    color: white;
  }

  /* Change the link color on hover */
  li div:hover {
    background-color: grey;
    color: white;
  }
}
</style>
