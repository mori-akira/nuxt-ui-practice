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
        <AppUserMenu />
      </template>

      <template #content="{ close }">
        <AppNavigationMenu :close="close" />
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
