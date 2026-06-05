<template>
  <svg
    :width="width"
    :height="height"
    :class="className"
    viewBox="0 0 24 16"
    aria-hidden="true"
    :style="{
      display: 'block',
      borderRadius: '1.5px',
      flexShrink: '0',
      ...(lang === 'en' ? { overflow: 'hidden' } : {})
    }"
  >
    <!-- ES: España -->
    <g v-if="lang === 'es'">
      <rect x="0" y="0" width="24" height="4" fill="#AA151B" />
      <rect x="0" y="4" width="24" height="8" fill="#F1BF00" />
      <rect x="0" y="12" width="24" height="4" fill="#AA151B" />
    </g>

    <!-- AR: Arabia -->
    <g v-else-if="lang === 'ar'">
      <rect width="24" height="16" fill="#006C35" />
      <path d="M6,8 h2 v-2 M9,8 h3 v-1 M13,8 h5" stroke="#FFFFFF" fill="none" stroke-width="1.2" stroke-linecap="round" stroke-linejoin="round" />
      <path d="M11,5 v2 M15,5 v2" stroke="#FFFFFF" fill="none" stroke-width="1.2" stroke-linecap="round" />
      <circle cx="8" cy="5" r="0.8" fill="#FFFFFF" />
      <path d="M5,12 h12 c0,0 1,-0.5 2,-1.5 v2.5 c-1,-1 -2,-1.5 -2,-1.5 h-12 c-1,0 -2,-0.5 -2,-0.5 s1,1 2,1 z" fill="#FFFFFF" />
      <rect x="17" y="10.5" width="1.5" height="3" rx="0.5" fill="#FFFFFF" />
    </g>

    <!-- DE: Alemania -->
    <g v-else-if="lang === 'de'">
      <rect x="0" y="0" width="24" height="16" fill="#FFCE00" />
      <rect x="0" y="0" width="24" height="10.67" fill="#DD0000" />
      <rect x="0" y="0" width="24" height="5.33" fill="#000000" />
    </g>

    <!-- EN: Reino Unido / Inglés -->
    <g v-else-if="lang === 'en'">
      <rect width="24" height="16" fill="#012169" />
      <path d="M0,0 L24,16 M24,0 L0,16" stroke="#FFFFFF" stroke-width="3" />
      <path d="M0,0 L24,16 M24,0 L0,16" stroke="#C8102E" stroke-width="1.5" />
      <path d="M12,0 V16 M0,8 H24" stroke="#FFFFFF" stroke-width="4" />
      <path d="M12,0 V16 M0,8 H24" stroke="#C8102E" stroke-width="2.5" />
    </g>

    <!-- FR: Francia -->
    <g v-else-if="lang === 'fr'">
      <rect x="0" y="0" width="24" height="16" fill="#ED2939" />
      <rect x="0" y="0" width="16" height="16" fill="#FFFFFF" />
      <rect x="0" y="0" width="8" height="16" fill="#002395" />
    </g>

    <!-- IT: Italia -->
    <g v-else-if="lang === 'it'">
      <rect x="0" y="0" width="24" height="16" fill="#CE2B37" />
      <rect x="0" y="0" width="16" height="16" fill="#FFFFFF" />
      <rect x="0" y="0" width="8" height="16" fill="#009246" />
    </g>

    <!-- PT: Portugal -->
    <g v-else-if="lang === 'pt'">
      <rect x="0" y="0" width="24" height="16" fill="#DA291C" />
      <rect x="0" y="0" width="9.6" height="16" fill="#046A38" />
      <circle cx="9.6" cy="8" r="3.5" fill="#F4C300" />
      <path d="M8.1,6.5 h3 v1.5 a1.5,1.5 0 0,1 -1.5,1.5 a1.5,1.5 0 0,1 -1.5,-1.5 z" fill="#FFFFFF" stroke="#DA291C" stroke-width="0.5" />
    </g>
  </svg>
</template>

<script setup lang="ts">
interface Props {
  lang: string;
  width?: number | string;
  height?: number | string;
  className?: string;
}

withDefaults(defineProps<Props>(), {
  width: 48,
  height: 32,
  className: ''
});
</script>
