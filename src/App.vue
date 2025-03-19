<script setup lang="ts">
function closeTab() {
  window.close()
}
</script>

<template>
  <div class="background">
    <div class="window glass active full-height">
      <div class="title-bar">
        <div class="title-bar-text">Atirut's Portfolio</div>
        <div class="title-bar-controls">
          <button aria-label="Close" @click="closeTab"></button>
        </div>
      </div>
      <div class="window-body has-space scrollable">
        <h3>Hello, I'm Atirut!</h3>
        <img src="./assets/portrait.jpg" alt="Portrait" class="portrait" />
      </div>
    </div>
  </div>
</template>

<style>
body {
  background-image: url('./assets/wallpaper.jpg');
  background-size: cover;
  margin: 0;
  padding: 0;
  height: 100vh;
}

.background {
  height: 100vh;
  padding: 1rem;
  box-sizing: border-box;
}

.full-height {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.scrollable {
  flex: 1;
  overflow-y: auto;
}

.portrait {
  max-width: 300px;
  max-height: 300px;
  display: block;
  border-radius: 8px;
}
</style>
