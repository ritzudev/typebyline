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

    <!-- EN: Reino Unido / Inglés -->
    <g v-else-if="lang === 'en'">
      <rect width="24" height="16" fill="#012169" />
      <path d="M0,0 L24,16 M24,0 L0,16" stroke="#FFFFFF" stroke-width="3" />
      <path d="M0,0 L24,16 M24,0 L0,16" stroke="#C8102E" stroke-width="1.5" />
      <path d="M12,0 V16 M0,8 H24" stroke="#FFFFFF" stroke-width="4" />
      <path d="M12,0 V16 M0,8 H24" stroke="#C8102E" stroke-width="2.5" />
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
