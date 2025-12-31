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
