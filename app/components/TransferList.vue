<template>
  <div class="grid grid-cols-[1fr_auto_1fr] gap-4 items-start">
    <!-- Left -->
    <UCard>
      <template #header>
        <div class="flex items-center justify-between gap-2">
          <div class="font-medium">{{ leftTitle }}</div>
          <UBadge color="neutral" variant="soft">{{ leftItems.length }}</UBadge>
        </div>

        <div v-if="searchable" class="mt-3">
          <UInput v-model="leftQuery" placeholder="検索（key / label）" />
        </div>
      </template>

      <UScrollArea :style="{ height: `${heightPx}px` }">
        <div class="flex flex-col gap-1 pr-2">
          <label
            v-for="item in leftItemsFiltered"
            :key="item.key"
            class="flex items-center justify-between gap-3 rounded-md border px-3 py-2 select-none"
            :class="rowClass(item, 'left')"
            tabindex="0"
            @click.prevent="onRowClick('left', item)"
            @keydown.enter.prevent="onRowClick('left', item)"
            @keydown.space.prevent="onRowClick('left', item)"
          >
            <div class="min-w-0 flex-1">
              <div class="truncate font-medium">{{ item.label }}</div>
            </div>

            <UCheckbox
              :model-value="leftChecked.includes(item.key)"
              :disabled="disabled || !!item.disabled"
              @update:model-value="(v) => setChecked('left', item.key, !!v)"
              @click.stop
            />
          </label>

          <div
            v-if="leftItemsFiltered.length === 0"
            class="text-sm text-muted p-3"
          >
            該当する項目がありません
          </div>
        </div>
      </UScrollArea>
    </UCard>

    <!-- Buttons -->
    <div class="flex flex-col gap-2 pt-8">
      <UButton
        icon="i-lucide-chevron-right"
        :disabled="disabled || leftChecked.length === 0"
        @click="moveRight"
      />
      <UButton
        icon="i-lucide-chevron-left"
        :disabled="disabled || rightChecked.length === 0"
        @click="moveLeft"
      />
    </div>

    <!-- Right -->
    <UCard>
      <template #header>
        <div class="flex items-center justify-between gap-2">
          <div class="font-medium">{{ rightTitle }}</div>
          <UBadge color="neutral" variant="soft">{{
            rightItems.length
          }}</UBadge>
        </div>

        <div v-if="searchable" class="mt-3">
          <UInput v-model="rightQuery" placeholder="検索（key / label）" />
        </div>
      </template>

      <UScrollArea :style="{ height: `${heightPx}px` }">
        <div class="flex flex-col gap-1 pr-2">
          <label
            v-for="item in rightItemsFiltered"
            :key="item.key"
            class="flex items-center justify-between gap-3 rounded-md border px-3 py-2 select-none"
            :class="rowClass(item, 'right')"
            tabindex="0"
            @click.prevent="onRowClick('right', item)"
            @keydown.enter.prevent="onRowClick('right', item)"
            @keydown.space.prevent="onRowClick('right', item)"
          >
            <div class="min-w-0 flex-1">
              <div class="truncate font-medium">{{ item.label }}</div>
              <div class="mt-0.5 truncate font-mono text-xs text-muted">
                {{ item.key }}
              </div>
            </div>

            <UCheckbox
              :model-value="rightChecked.includes(item.key)"
              :disabled="disabled || !!item.disabled"
              @update:model-value="(v) => setChecked('right', item.key, !!v)"
              @click.stop
            />
          </label>

          <div
            v-if="rightItemsFiltered.length === 0"
            class="text-sm text-muted p-3"
          >
            該当する項目がありません
          </div>
        </div>
      </UScrollArea>
    </UCard>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, watch } from "vue";

type TransferItem = {
  key: string;
  label: string;
  disabled?: boolean;
};

type Props = {
  /** 全体の要素（Key:Label） */
  items: TransferItem[];
  /** 選択済み要素（key配列） */
  modelValue?: string[];

  /** 任意 */
  leftTitle?: string;
  rightTitle?: string;
  heightPx?: number; // リスト部分の高さ
  searchable?: boolean;
  disabled?: boolean;
};

const props = withDefaults(defineProps<Props>(), {
  modelValue: () => [],
  leftTitle: "未選択",
  rightTitle: "選択済み",
  heightPx: 320,
  searchable: true,
  disabled: false,
});

const emit = defineEmits<{
  /** v-model */
  (e: "update:modelValue", value: string[]): void;
  /** 要件の「選択済み要素の変更」用（好みで使う） */
  (e: "change", value: string[]): void;
}>();

/** --- 内部状態（左右で “チェックされてる＝移動対象”） --- */
const leftChecked = ref<string[]>([]);
const rightChecked = ref<string[]>([]);
const leftQuery = ref("");
const rightQuery = ref("");

/** --- 正規化（itemsに存在するkeyだけ & 重複排除） --- */
const keySet = computed(() => new Set(props.items.map((i) => i.key)));

function uniqInOrder(keys: string[]) {
  const seen = new Set<string>();
  const out: string[] = [];
  for (const k of keys) {
    if (!seen.has(k)) {
      seen.add(k);
      out.push(k);
    }
  }
  return out;
}

const selectedKeys = computed(() => {
  const filtered = (props.modelValue ?? []).filter((k) => keySet.value.has(k));
  return uniqInOrder(filtered);
});

const selectedSet = computed(() => new Set(selectedKeys.value));

/** itemsの順序を維持して左右に分配 */
const leftItems = computed(() =>
  props.items.filter((i) => !selectedSet.value.has(i.key))
);
const rightItems = computed(() =>
  props.items.filter((i) => selectedSet.value.has(i.key))
);

function includesQuery(i: TransferItem, q: string) {
  if (!q) return true;
  const nq = q.toLowerCase();
  return i.key.toLowerCase().includes(nq) || i.label.toLowerCase().includes(nq);
}

const leftItemsFiltered = computed(() =>
  leftItems.value.filter((i) => includesQuery(i, leftQuery.value))
);
const rightItemsFiltered = computed(() =>
  rightItems.value.filter((i) => includesQuery(i, rightQuery.value))
);

/** 選択状態が変わったら、移動対象チェックを掃除（存在しない/側が変わったkeyを消す） */
watch(
  () => [props.items, selectedKeys.value] as const,
  () => {
    const leftKeySet = new Set(leftItems.value.map((i) => i.key));
    const rightKeySet = new Set(rightItems.value.map((i) => i.key));
    leftChecked.value = leftChecked.value.filter((k) => leftKeySet.has(k));
    rightChecked.value = rightChecked.value.filter((k) => rightKeySet.has(k));
  },
  { deep: true }
);

function emitSelection(next: string[]) {
  const normalized = uniqInOrder(next.filter((k) => keySet.value.has(k)));
  emit("update:modelValue", normalized);
  emit("change", normalized);
}

function rowClass(item: TransferItem, side: "left" | "right") {
  const isDisabled = props.disabled || !!item.disabled;
  const checked =
    side === "left"
      ? leftChecked.value.includes(item.key)
      : rightChecked.value.includes(item.key);

  return [
    isDisabled
      ? "cursor-not-allowed opacity-60"
      : "cursor-pointer hover:bg-gray-50",
    checked ? "border-gray-400 bg-gray-50" : "border-default",
  ];
}

/** rowクリック（行全体でトグル） */
function onRowClick(side: "left" | "right", item: TransferItem) {
  if (props.disabled || item.disabled) return;

  const arr = side === "left" ? leftChecked.value : rightChecked.value;
  const has = arr.includes(item.key);
  const next = has ? arr.filter((k) => k !== item.key) : [...arr, item.key];

  if (side === "left") leftChecked.value = next;
  else rightChecked.value = next;
}

/** チェックボックス自体での更新（rowクリックと同じ結果に寄せる） */
function setChecked(side: "left" | "right", key: string, v: boolean) {
  if (props.disabled) return;

  const arr = side === "left" ? leftChecked.value : rightChecked.value;
  const has = arr.includes(key);

  const next =
    v && !has ? [...arr, key] : !v && has ? arr.filter((k) => k !== key) : arr;

  if (side === "left") leftChecked.value = next;
  else rightChecked.value = next;
}

function moveRight() {
  if (props.disabled) return;

  const leftMap = new Map(leftItems.value.map((i) => [i.key, i]));
  const toAdd = leftChecked.value
    .map((k) => leftMap.get(k))
    .filter((i): i is TransferItem => !!i)
    .filter((i) => !i.disabled)
    .map((i) => i.key);

  if (toAdd.length === 0) return;

  emitSelection([...selectedKeys.value, ...toAdd]);
  leftChecked.value = [];
}

function moveLeft() {
  if (props.disabled) return;

  const rightMap = new Map(rightItems.value.map((i) => [i.key, i]));
  const toRemove = new Set(
    rightChecked.value
      .map((k) => rightMap.get(k))
      .filter((i): i is TransferItem => !!i)
      .filter((i) => !i.disabled)
      .map((i) => i.key)
  );

  if (toRemove.size === 0) return;

  emitSelection(selectedKeys.value.filter((k) => !toRemove.has(k)));
  rightChecked.value = [];
}
</script>
