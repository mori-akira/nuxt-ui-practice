<template>
  <UDropdownMenu
    :items="userMenuItems"
    :content="{ align: 'end', side: 'bottom', sideOffset: 8 }"
    :ui="{ content: 'w-56' }"
  >
    <UButton
      color="neutral"
      variant="ghost"
      aria-label="User menu"
      class="p-2 rounded-full cursor-pointer"
    >
      <span>{{ user.name }}</span>
    </UButton>

    <template #content-top>
      <div class="p-2">
        <UUser :name="user.name" :description="user.email" />
      </div>
    </template>
  </UDropdownMenu>
</template>

<script setup lang="ts">
import type { DropdownMenuItem } from "@nuxt/ui";

const user = reactive({
  name: "mori-akira",
  email: "mori-akira@example.com",
});

async function signOut() {
  console.log("sign out");
  await navigateTo("/");
}

const userMenuItems = computed<DropdownMenuItem[][]>(() => [
  [
    {
      label: "プロフィール",
      icon: "i-lucide-user",
      onSelect: () => navigateTo("/"),
    },
    {
      label: "設定",
      icon: "i-lucide-settings",
      onSelect: () => navigateTo("/"),
    },
  ],
  [
    {
      label: "サインアウト",
      icon: "i-lucide-log-out",
      color: "error",
      onSelect: () => signOut(),
    },
  ],
]);
</script>
