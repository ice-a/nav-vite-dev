<template>
  <div class="search-box-centered">
    <input v-model="searchText" @input="onInput" placeholder="搜索站点/描述/分类" class="search-input" :style="inputStyle" ref="inputRef" />
    <span v-if="searchText" class="search-clear" @click="clearSearch">×</span>
    <div v-if="highlightWords && highlightWords.length && searchText" class="search-highlight-preview">
      <span v-html="highlightInput(searchText, highlightWords)"></span>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, defineProps, defineEmits } from 'vue';
const props = defineProps({
  modelValue: String,
  highlightWords: {
    type: Array,
    default: () => []
  }
});
const emit = defineEmits(['update:modelValue', 'search']);
const searchText = ref(props.modelValue || '');
const inputRef = ref(null);
const inputStyle = ref('position:relative; background:transparent;');

watch(() => props.modelValue, (val) => {
  searchText.value = val;
});

function onInput() {
  emit('update:modelValue', searchText.value);
  emit('search', searchText.value);
}
function clearSearch() {
  searchText.value = '';
  emit('update:modelValue', '');
  emit('search', '');
}
function highlightInput(text, words) {
  if (!words || !words.length) return text;
  let pattern = words.filter(Boolean).map(w => w.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')).join('|');
  if (!pattern) return text;
  let reg = new RegExp(`(${pattern})`, 'gi');
  return text.replace(reg, '<span class="highlight">$1</span>');
}
</script>

<style scoped>
.search-box-centered {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 18px 0 0 0;
  position: relative;
}
.search-input {
  height: 32px;
  border-radius: 16px;
  border: 1px solid #e0e0e0;
  padding: 0 36px 0 16px;
  font-size: 15px;
  outline: none;
  transition: border 0.2s;
  min-width: 260px;
  background: transparent;
}
.search-input:focus {
  border: 1.5px solid #36d1c4;
}
.search-clear {
  position: absolute;
  right: 10px;
  color: #aaa;
  font-size: 18px;
  cursor: pointer;
  user-select: none;
}
.search-highlight-preview {
  position: absolute;
  left: 0;
  top: 36px;
  width: 100%;
  pointer-events: none;
  color: #333;
  font-size: 15px;
  background: #fffbe6;
  border-radius: 6px;
  padding: 2px 8px;
  z-index: 10;
}
.highlight {
  background: #ffe58f;
  color: #d46b08;
  border-radius: 2px;
  padding: 0 2px;
}
@media (max-width: 768px) {
  .search-box-centered {
    margin: 10px 0 0 0;
  }
  .search-input {
    min-width: 180px;
    font-size: 13px;
    height: 28px;
    padding: 0 30px 0 10px;
  }
  .search-clear {
    font-size: 16px;
    right: 6px;
  }
}
@media (max-width: 480px) {
  .search-box-centered {
    margin: 6px 0 0 0;
  }
  .search-input {
    min-width: 120px;
    font-size: 12px;
    height: 24px;
    padding: 0 24px 0 8px;
  }
  .search-clear {
    font-size: 14px;
    right: 4px;
  }
}
</style>