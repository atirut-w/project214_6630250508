<script setup lang="ts">
import Window from './components/Window.vue'
</script>

<template>
  <div class="background">
    <Window title="Atirut's Portfolio" :fullHeight="true">
      <h3>Hello, I'm Atirut!</h3>
      <img src="./assets/portrait.jpg" alt="Portrait" class="portrait" />
    </Window>
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

.portrait {
  max-width: 300px;
  max-height: 300px;
  display: block;
  border-radius: 8px;
}
</style>
