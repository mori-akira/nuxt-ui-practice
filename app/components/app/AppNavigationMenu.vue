<template>
  <div class="w-full h-full flex flex-col">
    <div class="h-(--ui-header-height) flex items-center justify-start px-4">
      <UButton
        color="neutral"
        variant="ghost"
        icon="i-lucide-x"
        aria-label="Close menu"
        @click="close?.()"
      />
      <h2 class="text-gray-800 font-bold ml-4">メニュー</h2>
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

<script setup lang="ts">
import type { NavigationMenuItem } from "@nuxt/ui";

defineProps({
  close: {
    type: Function as PropType<() => void>,
    required: false,
  },
});
const route = useRoute();
const menuItems = computed<NavigationMenuItem[]>(() => [
  {
    label: "ホーム",
    to: "/",
    active: route.path === "/",
  },
  {
    label: "デュアルリスト",
    to: "/transfer-list",
    active: route.path.startsWith("/transfer-list"),
  },
]);
</script>
