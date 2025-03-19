<script setup lang="ts">
defineProps<{
  title: string
  fullHeight?: boolean
}>()

function closeTab() {
  window.close()
}
</script>

<template>
  <div class="window glass active" :class="{ 'full-height': fullHeight }">
    <div class="title-bar">
      <div class="title-bar-text">{{ title }}</div>
      <div class="title-bar-controls">
        <button aria-label="Close" @click="closeTab"></button>
      </div>
    </div>
    <div class="window-body has-space" :class="{ scrollable: fullHeight }">
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.full-height {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.scrollable {
  flex: 1;
  overflow-y: auto;
}
</style>
