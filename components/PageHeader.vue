<script setup lang="ts">
const props = defineProps({
  title: {
    type: String,
    default: 'SSH Config Generator',
  },
  breadcrumbs: {
    type: Array,
    default: () => [],
  },
})
</script>

<template>
  <h1 class="text-5xl font-medium tracking-wide">{{ title }}</h1>
</template>

<style scoped>

</style>