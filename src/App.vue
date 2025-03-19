<script setup lang="ts">
import Window from './components/Window.vue'
import ProfileSection from './components/ProfileSection.vue'
</script>

<template>
  <div class="background">
    <Window title="Atirut's Portfolio" :fullHeight="true">
      <div class="content">
        <div class="header">
          <h1>Hello, I'm Atirut!</h1>
          <p class="subtitle">Computer Engineering Student</p>
        </div>

        <ProfileSection />

        <div class="interests-section">
          <h3>Interests</h3>
          <ul class="interests-list">
            <li>Open Source Software</li>
            <li>Low-Level Programming</li>
            <li>Game Development</li>
            <li>Computer Graphics</li>
            <li>Gaming</li>
            <li>Software Localization</li>
          </ul>
        </div>
      </div>
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
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.background {
  height: 100vh;
  padding: 1rem;
  box-sizing: border-box;
}

.content {
  padding: 1rem;
}

.header {
  text-align: center;
  margin-bottom: 2rem;
}

.header h1 {
  margin-bottom: 0.5rem;
}

.subtitle {
  color: #666;
  font-size: 1.1rem;
  margin-top: 0;
}

.profile-section {
  display: flex;
  flex-wrap: wrap;
  gap: 2rem;
  justify-content: center;
  align-items: flex-start;
  margin-bottom: 2rem;
}

.portrait {
  max-width: 250px;
  max-height: 250px;
  border-radius: 8px;
  border: 1px solid #ccc;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.info-card {
  min-width: 300px;
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 1rem;
  border: 1px solid #ccc;
}

.info-card h3 {
  margin-top: 0;
  margin-bottom: 1rem;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.info-row {
  display: flex;
  margin-bottom: 0.5rem;
}

.info-label {
  font-weight: bold;
  width: 100px;
}

.interests-section {
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 1rem;
  border: 1px solid #ccc;
}

.interests-section h3 {
  margin-top: 0;
  margin-bottom: 1rem;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.interests-list {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  padding: 0;
  list-style: none;
}

.interests-list li {
  background-color: #e9e9e9;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  border: 1px solid #ddd;
}
</style>
