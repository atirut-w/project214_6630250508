<script setup lang="ts">
import { ref, reactive, onMounted, computed } from 'vue'

interface ClassRegister {
  id?: number
  code: string
  name: string
  credits: number
  grade: string
}

const classes = ref<ClassRegister[]>([])
const isEditing = ref(false)
const newClass = reactive<ClassRegister>({
  code: '',
  name: '',
  credits: 3,
  grade: '',
})
const isAddingNew = ref(false)
const editingIndex = ref(-1)

// Valid grade options
const gradeOptions = ['A', 'B+', 'B', 'C+', 'C', 'D+', 'D', 'F', 'W']

// Computed property for GPA
const gpa = computed(() => {
  if (classes.value.length === 0) return 0

  const gradePoints: Record<string, number> = {
    A: 4,
    'B+': 3.5,
    B: 3,
    'C+': 2.5,
    C: 2,
    'D+': 1.5,
    D: 1,
    F: 0,
  }

  let totalPoints = 0
  let totalCredits = 0

  classes.value.forEach((cls) => {
    if (cls.grade !== 'W' && gradePoints[cls.grade] !== undefined) {
      totalPoints += gradePoints[cls.grade] * cls.credits
      totalCredits += cls.credits
    }
  })

  return totalCredits === 0 ? 0 : parseFloat((totalPoints / totalCredits).toFixed(2))
})

// Load class registers data
onMounted(async () => {
  try {
    const response = await fetch('http://localhost:3000/classRegists')
    if (response.ok) {
      const data = await response.json()
      classes.value = data
    }
  } catch (error) {
    console.error('Failed to load class registers:', error)
  }
})

function startAddingClass() {
  isAddingNew.value = true
  Object.assign(newClass, {
    code: '',
    name: '',
    credits: 3,
    grade: '',
  })
}

function cancelAddingClass() {
  isAddingNew.value = false
}

async function saveNewClass() {
  try {
    // Simple validation
    if (!newClass.code || !newClass.name || !newClass.grade) {
      alert('Please fill in all required fields.')
      return
    }

    const response = await fetch('http://localhost:3000/classRegists', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newClass),
    })

    if (response.ok) {
      const savedClass = await response.json()
      classes.value.push(savedClass)
      isAddingNew.value = false
    }
  } catch (error) {
    console.error('Failed to add class:', error)
  }
}

function startEditingClass(index: number) {
  editingIndex.value = index
}

function cancelEditingClass() {
  editingIndex.value = -1
}

async function updateClass(index: number) {
  const classToUpdate = classes.value[index]

  // Simple validation
  if (!classToUpdate.code || !classToUpdate.name || !classToUpdate.grade) {
    alert('Please fill in all required fields.')
    return
  }

  try {
    const response = await fetch(`http://localhost:3000/classRegists/${classToUpdate.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(classToUpdate),
    })

    if (response.ok) {
      editingIndex.value = -1
    }
  } catch (error) {
    console.error('Failed to update class:', error)
  }
}

async function deleteClass(index: number) {
  const classToDelete = classes.value[index]

  if (!confirm(`Are you sure you want to delete ${classToDelete.code}?`)) {
    return
  }

  try {
    const response = await fetch(`http://localhost:3000/classRegists/${classToDelete.id}`, {
      method: 'DELETE',
    })

    if (response.ok) {
      classes.value.splice(index, 1)
    }
  } catch (error) {
    console.error('Failed to delete class:', error)
  }
}

function toggleEditMode() {
  isEditing.value = !isEditing.value
  if (!isEditing.value) {
    isAddingNew.value = false
    editingIndex.value = -1
  }
}
</script>

<template>
  <div class="classes-section">
    <div class="section-header">
      <h3>Class Registrations</h3>
      <button v-if="!isEditing" @click="toggleEditMode" class="edit-btn">Edit</button>
      <button v-else @click="toggleEditMode" class="edit-btn done-btn">Done</button>
    </div>

    <div v-if="classes.length === 0" class="empty-state">No classes registered yet.</div>

    <table v-else class="classes-table">
      <thead>
        <tr>
          <th>Code</th>
          <th>Name</th>
          <th>Credits</th>
          <th>Grade</th>
          <th v-if="isEditing">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(cls, index) in classes"
          :key="index"
          :class="{ 'editing-row': editingIndex === index }"
        >
          <template v-if="editingIndex === index">
            <!-- Edit mode for existing class -->
            <td><input v-model="cls.code" class="table-input code-input" /></td>
            <td><input v-model="cls.name" class="table-input name-input" /></td>
            <td>
              <input
                v-model.number="cls.credits"
                type="number"
                min="1"
                max="6"
                class="table-input credit-input"
              />
            </td>
            <td>
              <select v-model="cls.grade" class="table-input grade-input">
                <option value="" disabled>Select Grade</option>
                <option v-for="grade in gradeOptions" :key="grade" :value="grade">
                  {{ grade }}
                </option>
              </select>
            </td>
            <td class="action-buttons">
              <button @click="updateClass(index)" class="save-btn">Save</button>
              <button @click="cancelEditingClass" class="cancel-btn">Cancel</button>
            </td>
          </template>
          <template v-else>
            <!-- Display mode -->
            <td>{{ cls.code }}</td>
            <td>{{ cls.name }}</td>
            <td>{{ cls.credits }}</td>
            <td>{{ cls.grade }}</td>
            <td v-if="isEditing" class="action-buttons">
              <button @click="startEditingClass(index)" class="edit-row-btn">Edit</button>
              <button @click="deleteClass(index)" class="delete-btn">Delete</button>
            </td>
          </template>
        </tr>
      </tbody>
      <tfoot v-if="classes.length > 0">
        <tr class="summary-row">
          <td colspan="2">GPA</td>
          <td colspan="3" class="gpa-value">{{ gpa }}</td>
        </tr>
      </tfoot>
    </table>

    <!-- Form to add new class -->
    <div v-if="isEditing && isAddingNew" class="add-class-form">
      <h4>Add New Class</h4>
      <div class="form-row">
        <label>
          Code:
          <input v-model="newClass.code" placeholder="e.g. SC123456" />
        </label>
      </div>
      <div class="form-row">
        <label>
          Name:
          <input v-model="newClass.name" placeholder="Class Name" />
        </label>
      </div>
      <div class="form-row">
        <label>
          Credits:
          <input v-model.number="newClass.credits" type="number" min="1" max="6" />
        </label>
      </div>
      <div class="form-row">
        <label>
          Grade:
          <select v-model="newClass.grade">
            <option value="" disabled selected>Select Grade</option>
            <option v-for="grade in gradeOptions" :key="grade" :value="grade">{{ grade }}</option>
          </select>
        </label>
      </div>
      <div class="form-actions">
        <button @click="saveNewClass" class="save-btn">Add Class</button>
        <button @click="cancelAddingClass" class="cancel-btn">Cancel</button>
      </div>
    </div>

    <!-- Button to start adding a new class -->
    <div v-if="isEditing && !isAddingNew" class="add-class-container">
      <button @click="startAddingClass" class="add-class-btn">+ Add Class</button>
    </div>
  </div>
</template>

<style scoped>
.classes-section {
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 1rem;
  border: 1px solid #ccc;
  margin-bottom: 2rem;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.section-header h3 {
  margin: 0;
}

.empty-state {
  text-align: center;
  color: #666;
  padding: 1rem;
}

.classes-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 1rem;
}

.classes-table th,
.classes-table td {
  padding: 0.5rem;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.classes-table th {
  background-color: #eee;
  font-weight: bold;
}

.editing-row {
  background-color: #f8f8f8;
}

.summary-row {
  font-weight: bold;
  background-color: #eee;
}

.gpa-value {
  text-align: right;
}

.table-input {
  padding: 0.3rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.code-input {
  width: 80px;
}

.name-input {
  width: 100%;
}

.credit-input {
  width: 50px;
}

.action-buttons {
  display: flex;
  gap: 0.5rem;
}

.add-class-container {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

.add-class-form {
  background-color: #eee;
  padding: 1rem;
  border-radius: 6px;
  margin-top: 1rem;
  border: 1px solid #ddd;
}

.add-class-form h4 {
  margin-top: 0;
  margin-bottom: 1rem;
}

.form-row {
  margin-bottom: 0.75rem;
}

.form-row label {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.form-row input,
.form-row select {
  padding: 0.3rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.form-actions {
  display: flex;
  gap: 0.5rem;
  margin-top: 1rem;
}

.edit-btn,
.save-btn,
.cancel-btn,
.edit-row-btn,
.delete-btn,
.add-class-btn {
  /* padding: 0.25rem 0.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem; */
}

.edit-btn {
  /* background-color: #e0e0e0;
  border: 1px solid #ccc; */
}

.done-btn {
  /* background-color: #2196F3;
  color: white;
  border: 1px solid #1976D2; */
}

.save-btn {
  /* background-color: #4CAF50;
  color: white;
  border: 1px solid #388E3C; */
}

.cancel-btn {
  /* background-color: #f44336;
  color: white;
  border: 1px solid #d32f2f; */
}

.edit-row-btn {
  /* background-color: #FFC107;
  border: 1px solid #FFA000; */
}

.delete-btn {
  background-color: #f44336;
  color: white;
  border: 1px solid #d32f2f;
}

.add-class-btn {
  /* background-color: #4caf50;
  color: white;
  border: 1px solid #388e3c;
  padding: 0.5rem 1rem; */
}
</style>
