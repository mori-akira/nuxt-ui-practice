<script setup lang="ts">
const allItems = ref(
  Array.from({ length: 100 }, (_, i) => i + 1).map((i) => ({
    key: i.toString(),
    label: `Item ${i}`,
  }))
);

const selected = ref<string[]>(["1"]);
</script>

<template>
  <TransferList
    :items="allItems"
    v-model="selected"
    left-title="未付与"
    right-title="付与済み"
  />
</template>
