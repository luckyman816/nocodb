<script setup lang="ts">
import { isDrawerOrModalExist, isMac, useNuxtApp, useRoles } from '#imports'
interface Props {
  disabled?: boolean
  isViewToolbar?: boolean
}
const { isMobileMode, getMainUrl } = useGlobal()

const { visibility, showEnrichModal } = storeToRefs(useShare())

const { activeTable } = storeToRefs(useTablesStore())

const { base, isSharedBase } = storeToRefs(useBase())
const { disabled, isViewToolbar } = defineProps<Props>()
interface Props {
  disabled?: boolean
  isViewToolbar?: boolean
}
const { isUIAllowed } = useRoles()
</script>
<template>
    <div
      v-if="!isSharedBase && isUIAllowed('baseShare') && visibility !== 'hidden' && (activeTable || base)"
      class="flex flex-col justify-center h-full"
      data-testid="share-base-button"
      :data-sharetype="visibility"
    >
      <NcButton
        v-e="['c:share:open']"
        :size="isMobileMode ? 'medium' : 'small'"
        class="z-10 !rounded-lg"
        :class="{
          '!px-2': !isMobileMode,
          '!px-0 !max-w-8.5 !min-w-8.5': isMobileMode,
        }"
        type="primary"
        :disabled="disabled"
        @click="showEnrichModal = true"
      >
        <div v-if="!isMobileMode" class="flex flex-row items-center w-full gap-x-1">
          <MdiPlus v-if="visibility === 'public'" class="h-3.5" />
          <MdiPlus v-else-if="visibility === 'private'" class="h-3.5" />
          <div class="flex">Enrich Data</div>
        </div>
        <GeneralIcon v-else icon="mobileShare" />
      </NcButton>
    </div>
  <LazySmartsheetColumnEnrichView :is-view-toolbar="isViewToolbar" />
</template>
