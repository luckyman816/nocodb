<script lang="ts" setup>
const { hoverable } = defineProps<{
  type?: string
  hoverable?: boolean
}>()
</script>

<template>
  <svg
    version="1.0"
    xmlns="http://www.w3.org/2000/svg"
    width="25.000000pt"
    height="25.000000pt"
    viewBox="0 0 104.000000 125.000000"
    preserveAspectRatio="xMidYMid meet"
  >
    <g transform="translate(0.000000,125.000000) scale(0.100000,-0.100000)" fill="#34abeb" stroke="none">
      <path
        d="M486 988 c-19 -26 -31 -57 -34 -87 -3 -41 0 -50 30 -85 l34 -40 38
40 c33 35 36 43 33 85 -3 31 -14 60 -33 87 -16 23 -31 42 -34 42 -3 0 -18 -19
-34 -42z" fill="#345eeb"
      />
      <path
        d="M249 822 c6 -12 11 -56 11 -99 0 -138 24 -187 136 -275 36 -28 74
-67 84 -87 l20 -36 -3 185 c-2 165 -4 189 -23 225 -25 48 -102 90 -182 100
-52 7 -53 6 -43 -13z"
      />
      <path
        d="M698 826 c-63 -17 -114 -53 -135 -96 -15 -29 -18 -67 -21 -220 l-3
-185 25 40 c13 23 51 61 84 87 107 83 132 134 132 271 0 43 5 87 10 98 9 17 7
19 -17 18 -15 0 -49 -6 -75 -13z"
      />
      <path
        d="M109 548 c13 -36 52 -56 120 -63 l63 -7 -18 32 c-24 40 -35 46 -111
53 -61 6 -62 6 -54 -15z"
      />
      <path
        d="M851 561 c-52 -7 -66 -16 -93 -65 -9 -17 -6 -18 54 -11 68 7 106 27
119 63 9 24 13 24 -80 13z"
      />
      <path
        d="M14 405 c11 -54 58 -117 106 -141 40 -21 59 -23 177 -24 72 0 143 -4
157 -10 24 -9 26 -7 26 16 0 65 -73 156 -139 174 -20 5 -103 11 -184 12 l-149
3 6 -30z"
      />
      <path
        d="M849 431 c-149 -6 -157 -7 -195 -33 -51 -36 -84 -87 -91 -138 l-6
-41 27 11 c15 5 87 10 159 10 118 1 137 3 177 24 51 26 95 83 105 139 6 28 4
37 -6 35 -8 -1 -84 -4 -170 -7z"
      />
    </g>
  </svg>
</template>

<style scoped>
.nc-base-icon {
  @apply text-xl;
}
.nc-base-icon-hoverable {
  @apply cursor-pointer !hover:bg-gray-200 !hover:bg-opacity-50;
}
</style>
