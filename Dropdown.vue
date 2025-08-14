<template>
  <div class="floating-dropdown w-100" ref="dropdownRef">
    <div class="position-relative w-100">
      <!-- 顯示框（只讀，點擊/聚焦開啟） -->
      <input
        type="text"
        class="form-select"
        :placeholder="placeholder"
        :value="selectedDisplay"
        @focus="isOpen = true"
        @click.stop
        readonly
        style="cursor:pointer; background-color:#fff;"
      />

      <!-- 右側圖示：無值＝箭頭；有值＝紅色×（點擊清空） -->
      <button
        type="button"
        class="dropdown-icon-btn"
        @click.stop="selectedDisplay ? clearSelection() : (isOpen = !isOpen)"
        :title="selectedDisplay ? '清除' : '展開'"
      >
        <span v-if="selectedDisplay" class="clear-icon">×</span>
        <span v-else class="arrow-icon">▾</span>
      </button>

      <!-- 下拉面板 -->
      <div class="dropdown-menu w-100 mt-1 responsive-dropdown" :class="{ show: isOpen }">
        <input
          v-if="isOpen"
          type="text"
          class="form-control mb-2"
          v-model="searchQuery"
          :placeholder="searchPlaceholder"
          @click.stop
        />
        <div class="dropdown-items-container" v-if="filteredItems.length > 0">
          <button
            v-for="item in filteredItems"
            :key="itemKey(item)"
            class="dropdown-item text-start"
            :class="{ active: isActive(item) }"
            type="button"
            @click="selectItem(item)"
          >
            {{ displayOf(item) }}
          </button>
        </div>
        <div v-else class="no-results">{{ noResultsText }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, onMounted, onBeforeUnmount, ref, watch } from 'vue';

/** Props / Emits **/
const props = defineProps({
  items: { type: Array, required: true },
  placeholder: { type: String, default: '請選擇' },
  searchPlaceholder: { type: String, default: '輸入關鍵字搜尋...' },
  noResultsText: { type: String, default: '沒有匹配的結果' },
  customItemDisplay: { type: Function, default: null },
  modelValue: { type: [String, Object, Number], default: '' },
});
const emit = defineEmits(['update:modelValue', 'item-selected']);

/** State **/
const dropdownRef = ref(null);
const isOpen = ref(false);
const searchQuery = ref('');
const selectedItem = ref(props.modelValue);

/** Computed **/
const filteredItems = computed(() => {
  const q = (searchQuery.value || '').toLowerCase();
  if (!q) return props.items || [];
  return (props.items || []).filter((it) => {
    const text = String(props.customItemDisplay ? props.customItemDisplay(it) : it);
    return text.toLowerCase().includes(q);
  });
});

const selectedDisplay = computed(() => {
  const s = selectedItem.value;
  if (!s) return '';
  return props.customItemDisplay ? props.customItemDisplay(s) : s;
});

/** Utils **/
const displayOf = (it) => (props.customItemDisplay ? props.customItemDisplay(it) : it);
const isActive = (it) => it === selectedItem.value;
const itemKey = (it) => (typeof it === 'object' && it !== null ? JSON.stringify(it) : it);

/** Actions **/
function selectItem(item) {
  selectedItem.value = item;
  isOpen.value = false;
  emit('update:modelValue', item);
  emit('item-selected', item);
  searchQuery.value = '';
}

function clearSelection() {
  selectedItem.value = '';
  emit('update:modelValue', '');
  emit('item-selected', null);
  searchQuery.value = '';
  isOpen.value = false;
}

/** Outside click to close **/
function onDocClick(e) {
  const el = dropdownRef.value;
  if (el && !el.contains(e.target)) isOpen.value = false;
}
onMounted(() => document.addEventListener('click', onDocClick));
onBeforeUnmount(() => document.removeEventListener('click', onDocClick));

/** Sync from v-model **/
watch(
  () => props.modelValue,
  (v) => {
    selectedItem.value = v;
  }
);
</script>
