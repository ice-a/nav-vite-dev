<template>
  <div class="gradient-header" :style="{ background: gradient }">
    <slot></slot>
  </div>
</template>

<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  gradient: {
    type: String,
    required: true
  }
})
</script>

<style scoped>
.gradient-header {
  width: 100%;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-weight: bold;
  font-size: 18px;
  border-radius: 8px 8px 0 0;
  margin-bottom: 8px;
}
</style>