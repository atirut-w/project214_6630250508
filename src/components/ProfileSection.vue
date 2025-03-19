<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'

interface Profile {
  name: string
  studentId: string
  major: string
  comingFrom: string
}

const isEditing = ref(false)
const profile = reactive<Profile>({
  name: '',
  studentId: '',
  major: '',
  comingFrom: '',
})

// Load initial profile data
onMounted(async () => {
  try {
    const response = await fetch('http://localhost:3000/profile')
    if (response.ok) {
      const data = await response.json()
      Object.assign(profile, data)
    }
  } catch (error) {
    console.error('Failed to load profile:', error)
  }
})

async function saveProfile() {
  try {
    const response = await fetch('http://localhost:3000/profile', {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(profile),
    })

    if (response.ok) {
      isEditing.value = false
    }
  } catch (error) {
    console.error('Failed to save profile:', error)
  }
}

function toggleEdit() {
  isEditing.value = !isEditing.value
}

function cancelEdit() {
  isEditing.value = false
  // Reload data to discard changes
  fetch('http://localhost:3000/profile')
    .then((response) => response.json())
    .then((data) => Object.assign(profile, data))
    .catch((error) => console.error('Failed to reload profile:', error))
}
</script>

<template>
  <div class="profile-section">
    <img src="../assets/portrait.jpg" alt="Portrait" class="portrait" />

    <div class="info-card">
      <div class="info-header">
        <h3>About Me</h3>
        <button v-if="!isEditing" @click="toggleEdit" class="edit-btn">Edit</button>
        <div v-else class="edit-actions">
          <button @click="saveProfile" class="save-btn">Save</button>
          <button @click="cancelEdit" class="cancel-btn">Cancel</button>
        </div>
      </div>

      <div v-if="!isEditing" class="info-display">
        <div class="info-row">
          <span class="info-label">Full name:</span>
          <span class="info-value">{{ profile.name }}</span>
        </div>
        <div class="info-row">
          <span class="info-label">Student ID:</span>
          <span class="info-value">{{ profile.studentId }}</span>
        </div>
        <div class="info-row">
          <span class="info-label">Major:</span>
          <span class="info-value">{{ profile.major }}</span>
        </div>
        <div class="info-row">
          <span class="info-label">Coming from:</span>
          <span class="info-value">{{ profile.comingFrom }}</span>
        </div>
      </div>

      <div v-else class="info-edit">
        <div class="info-row">
          <label class="info-label" for="name">Full name:</label>
          <input id="name" v-model="profile.name" class="info-input" />
        </div>
        <div class="info-row">
          <label class="info-label" for="studentId">Student ID:</label>
          <input id="studentId" v-model="profile.studentId" class="info-input" />
        </div>
        <div class="info-row">
          <label class="info-label" for="major">Major:</label>
          <input id="major" v-model="profile.major" class="info-input" />
        </div>
        <div class="info-row">
          <label class="info-label" for="comingFrom">Coming from:</label>
          <input id="comingFrom" v-model="profile.comingFrom" class="info-input" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
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

.info-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.info-header h3 {
  margin: 0;
}

.info-row {
  display: flex;
  margin-bottom: 0.5rem;
}

.info-label {
  font-weight: bold;
  width: 100px;
}

.info-input {
  flex: 1;
  padding: 0.25rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.edit-btn,
.save-btn,
.cancel-btn {
  /* padding: 0.25rem 0.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem; */
}

.edit-btn {
  /* background-color: #e0e0e0;
  border: 1px solid #ccc; */
}

.save-btn {
  /* background-color: #4caf50;
  color: white;
  border: 1px solid #388e3c;
  margin-right: 0.5rem; */
}

.cancel-btn {
  /* background-color: #f44336;
  color: white;
  border: 1px solid #d32f2f; */
}

.edit-actions {
  display: flex;
}
</style>
