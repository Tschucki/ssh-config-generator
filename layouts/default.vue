<script setup lang="ts">
import { Toaster } from "@/Components/ui/sonner";
import { Icon } from "@iconify/vue";

defineProps({
  title: {
    type: String,
    default: "SSH Config Generator",
  },
  breadcrumbs: {
    type: Array,
    default: () => [],
  },
});
</script>

<template>
  <Toaster />
  <div class="bg-background">
    <div class="max-w-7xl mx-auto p-4">
      <div class="flex items-center justify-between">
        <PageHeader :title="title" :breadcrumbs="breadcrumbs" />
        <div class="flex items-center gap-2">
          <ThemeToggler />
          <a
            href="https://github.com/Tschucki/ssh-config-generator"
            target="_blank"
          >
            <Button variant="outline">
              <Icon
                icon="radix-icons:github-logo"
                class="h-[1.2rem] w-[1.2rem] text-foreground"
              />
            </Button>
          </a>
        </div>
      </div>
      <slot />
    </div>
  </div>
</template>

<style scoped></style>
