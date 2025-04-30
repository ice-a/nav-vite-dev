<script setup>
import GradientHeader from './components/GradientHeader.vue';
import { ref, computed, onMounted, watch } from 'vue';
import navData from './data/tools.json';
import SearchBox from './components/SearchBox.vue';

function getRandomGradient() {
  const colors = [
    '#ff9966', '#ff5e62', '#36d1c4', '#5b86e5', '#f7971e', '#ffd200', '#f857a6', '#ff5858', '#43cea2', '#185a9d', '#ff512f', '#dd2476', '#1d976c', '#93f9b9', '#fc466b', '#3f5efb', '#ee9ca7', '#ffdde1', '#00c3ff', '#ffff1c'
  ];
  const idx1 = Math.floor(Math.random() * colors.length);
  let idx2 = Math.floor(Math.random() * colors.length);
  while(idx2 === idx1) idx2 = Math.floor(Math.random() * colors.length);
  return `linear-gradient(90deg, ${colors[idx1]}, ${colors[idx2]})`;
}

const categories = computed(() => {
  const categorySet = new Set();
  navData.forEach(item => {
    categorySet.add(item.catelog);
  });
  return Array.from(categorySet);
});

const activeCategory = ref('');
const selectedKeys = ref([]);

// 搜索相关
const searchText = ref('');
const searchActive = ref(false);
const searchResults = ref([]);

function splitWords(text) {
  // 简单分词：按空格、中文、英文、数字分割
  return text.toLowerCase().split(/[^\u4e00-\u9fa5\w]+/).filter(Boolean);
}

function highlightKeyword(text, keyword) {
  if (!keyword) return text;
  // 支持多个关键词高亮
  let words = splitWords(keyword);
  let pattern = words.filter(Boolean).map(w => w.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')).join('|');
  if (!pattern) return text;
  let reg = new RegExp(`(${pattern})`, 'gi');
  return text.replace(reg, '<span class="highlight">$1</span>');
}

function doSearch(val) {
  const kw = (typeof val === 'string' ? val : searchText.value).trim();
  if (!kw) {
    searchActive.value = false;
    searchResults.value = [];
    return;
  }
  searchActive.value = true;
  const words = splitWords(kw);
  searchResults.value = navData.filter(item => {
    const target = [item.name, item.desc, item.catelog].join(' ').toLowerCase();
    return words.every(w => target.includes(w));
  });
}

const filteredData = computed(() => {
  if (searchActive.value) {
    return searchResults.value;
  }
  if (!activeCategory.value) {
    return [];
  }
  return navData.filter(item => item.catelog === activeCategory.value);
});

watch(activeCategory, (newValue) => {
  selectedKeys.value = [newValue];
  generateCardGradients();
  headerGradient.value = getRandomGradient();
  if (searchActive.value) {
    searchActive.value = false;
    searchText.value = '';
  }
});

onMounted(() => {
  if (categories.value.length > 0) {
    activeCategory.value = categories.value[0];
    selectedKeys.value = [categories.value[0]];
    generateCardGradients();
    headerGradient.value = getRandomGradient();
  }
});

const cardGradients = ref([]);
const headerGradient = ref(getRandomGradient());
function generateCardGradients() {
  cardGradients.value = filteredData.value.map(() => getRandomGradient());
}
</script>

<template>
  <div class="app-header">
    <div class="app-title">开发者导航</div>
    <div class="search-box-wrapper">
      <SearchBox v-model="searchText" @search="doSearch" :highlight-words="splitWords(searchText)" />
    </div>
  </div>
  <div class="nav-container">
    <!-- 左侧分类导航 -->
    <div class="category-sidebar">
      <h2>分类导航</h2>
      <div class="category-grid">
        <div v-for="(category, idx) in categories" :key="category" class="category-item" :class="{active: activeCategory === category}" @click="activeCategory = category">
          {{ category }}
        </div>
      </div>
    </div>
    <!-- 中间分隔区 -->
    <div class="divider"></div>
    <!-- 右侧内容区 -->
    <div class="content-area">
      <GradientHeader :gradient="headerGradient">{{ searchActive ? '搜索结果' : activeCategory }}</GradientHeader>
      <div class="site-cards-grid">
        <a-card v-for="(item, idx) in filteredData" :key="item.name" class="site-card" hoverable>
          <template #title>
            <a :href="item.url" target="_blank" v-html="highlightKeyword(item.name, searchActive ? searchText : '')"></a>
          </template>
          <template #extra>
            <a-tag color="blue">{{ item.catelog }}</a-tag>
          </template>
          <GradientHeader :gradient="cardGradients[idx]">
            <span v-html="highlightKeyword(item.name, searchActive ? searchText : '')"></span>
          </GradientHeader>
          <p class="desc-ellipsis" :title="item.desc">
            <span v-html="highlightKeyword(item.desc.length > 6 ? item.desc.slice(0, 6) + '...' : item.desc, searchActive ? searchText : '')"></span>
          </p>
        </a-card>
      </div>
      <div v-if="searchActive && filteredData.length === 0" class="no-result">未找到相关结果</div>
    </div>
  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
}
</style>

<style scoped>
.app-header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: auto;
  background: linear-gradient(90deg, #36d1c4, #5b86e5);
  display: flex;
  flex-direction: column;
  align-items: center;
  z-index: 100;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  padding-bottom: 8px;
}
.app-title {
  color: #fff;
  font-size: 22px;
  font-weight: bold;
  margin-left: 0;
  letter-spacing: 2px;
  margin-top: 8px;
}
.search-box-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
  margin-bottom: 0;
}
.search-box {
  margin-left: 32px;
  display: flex;
  align-items: center;
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
.highlight {
  background: #ffe58f;
  color: #d46b08;
  border-radius: 2px;
  padding: 0 2px;
}
.no-result {
  color: #999;
  text-align: center;
  margin-top: 40px;
  font-size: 18px;
}
.nav-container {
  display: flex;
  height: 100vh;
  overflow: hidden;
  padding-top: 56px;
}
.category-sidebar {
  width: 260px;
  background-color: #fff;
  padding: 32px 20px 20px 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  overflow-y: auto;
  margin-top: 16px;
}
.category-grid {
  display: grid;
  grid-template-columns: repeat(1, 1fr);
  gap: 16px 12px;
  margin-top: 16px;
}
.category-item {
  background: #f0f0f0;
  border-radius: 6px;
  padding: 10px 0;
  text-align: center;
  cursor: pointer;
  font-weight: 500;
  transition: background 0.2s, color 0.2s;
  width: 200px;
  margin: 0 auto;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.category-item.active, .category-item:hover {
  background: linear-gradient(90deg, #36d1c4, #5b86e5);
  color: #fff;
}
.divider {
  width: 1px;
  background-color: #e8e8e8;
}
.content-area {
  flex: 1;
  padding: 36px 32px 20px 32px;
  overflow-y: auto;
  margin-top: 16px;
}
.site-cards-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  margin-top: 20px;
}
.site-card {
  margin-bottom: 16px;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
.desc-ellipsis {
  max-width: 120px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: inline-block;
  vertical-align: middle;
  cursor: pointer;
}
h2 {
  margin-bottom: 20px;
  color: #1890ff;
  border-bottom: 1px solid #e8e8e8;
  padding-bottom: 10px;
}
@media (max-width: 1200px) {
  .site-cards-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  .category-grid {
    grid-template-columns: repeat(1, 1fr);
  }
  .category-item {
    width: 140px;
  }
  .category-sidebar {
    width: 180px;
  }
}
@media (max-width: 800px) {
  .app-header {
    height: 44px;
  }
  .app-title {
    font-size: 16px;
    margin-left: 12px;
  }
  .nav-container {
    flex-direction: column;
    padding-top: 44px;
  }
  .category-sidebar {
    width: 100%;
    box-shadow: none;
    padding: 10px;
    margin-top: 8px;
  }
  .divider {
    display: none;
  }
  .content-area {
    padding: 18px 6px 10px 6px;
    margin-top: 8px;
  }
  .site-cards-grid {
    grid-template-columns: 1fr;
  }
  .category-item {
    width: 100%;
    min-width: 80px;
  }
}
</style>
