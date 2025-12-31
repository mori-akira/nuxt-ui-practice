<template>
  <UApp>
    <UHeader
      toggle-side="left"
      mode="slideover"
      :ui="{
        container: '!max-w-full',
        toggle: '!flex',
        title: '!hidden',
        center: '!flex flex-1',
        content: '!block',
        overlay: '!block',
      }"
      :menu="{
        side: 'left',
        inset: true,
      }"
    >
      <div class="flex items-center justify-center gap-2 w-full">
        <ULink to="/" class="text-gray-800 text-2xl font-bold"
          >Nuxt UI Practice</ULink
        >
      </div>

      <template #right>
        <UTooltip text="Open on GitHub">
          <UButton
            color="neutral"
            variant="ghost"
            to="https://github.com/mori-akira/nuxt-ui-practice"
            target="_blank"
            icon="i-simple-icons-github"
            aria-label="GitHub"
          />
        </UTooltip>
      </template>

      <template #content="{ close }">
        <div class="h-full flex flex-col">
          <div
            class="h-(--ui-header-height) flex items-center justify-start px-4"
          >
            <UButton
              color="neutral"
              variant="ghost"
              icon="i-lucide-x"
              aria-label="Close menu"
              @click="close?.()"
            />
            <h2 class="text-gray-800 font-bold">メニュー</h2>
          </div>

          <div class="p-4 sm:p-6 overflow-y-auto">
            <UNavigationMenu
              :items="menuItems"
              orientation="vertical"
              class="-mx-2.5"
            />
          </div>
        </div>
      </template>
    </UHeader>

    <UMain>
      <NuxtPage />
    </UMain>

    <UFooter
      :ui="{
        root: 'fixed inset-x-0 bottom-0 z-50 border-t-1 border-default bg-white',
        container: '!p-2',
      }"
    >
      <template #right>
        <p class="text-sm">Version 1.0.0</p>
      </template>
    </UFooter>
  </UApp>
</template>

<script setup lang="ts">
import type { NavigationMenuItem } from "@nuxt/ui";

const route = useRoute();
const menuItems = computed<NavigationMenuItem[]>(() => [
  {
    label: "ホーム",
    to: "/",
    active: route.path === "/",
  },
]);
</script>

<style scoped>
div[data-side="left"] div[data-slot="right"] {
  display: none;
}
</style>
