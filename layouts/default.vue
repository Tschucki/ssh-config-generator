<script setup lang="ts">
defineProps({
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
  <div class="max-w-5xl mx-auto">
    <PageHeader :title="title" :breadcrumbs="breadcrumbs" />
    <slot />
  </div>
</template>

<style scoped>

</style>