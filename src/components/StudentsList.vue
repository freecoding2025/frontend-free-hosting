<template>
  <div class="students-container">
    <h2>Students from Backend</h2>
    <div v-if="loading" class="loading">Loading...</div>
    <div v-if="error" class="error">{{ error }}</div>
    <ul v-if="students.length > 0" class="students-list">
      <li v-for="student in students" :key="student.id" class="student-item">
        <h3>{{ student.firstName }} {{ student.lastName }}</h3>
        <p><strong>Email:</strong> {{ student.email }}</p>
        <p><strong>Enrollment #:</strong> {{ student.enrollmentNo }}</p>
      </li>
    </ul>
    <div v-else-if="!loading && !error">
      No students found.
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const API_URL = `${import.meta.env.VITE_API_BASE_URL}/api/students`;

const students = ref([]);
const loading = ref(true);
const error = ref(null);

async function fetchStudents() {
  try {
    const response = await fetch(API_URL);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    students.value = data.students; // The API returns { students: [...] }
  } catch (e) {
    error.value = `Failed to fetch students: ${e.message}. Make sure the backend is running and the endpoint is correct.`;
    console.error(e);
  } finally {
    loading.value = false;
  }
}

onMounted(() => {
  fetchStudents();
});
</script>

<style scoped>
/* Re-using styles similar to NotesList for consistency */
.students-container, .student-item, .loading, .error, .students-list {
  font-family: sans-serif;
  max-width: 800px;
  margin: 1rem auto;
  padding: 1rem;
}
.student-item {
  border: 1px solid #a2d2ff;
  border-radius: 8px;
  margin-bottom: 1rem;
}
.student-item h3 {
  margin-top: 0;
  color: #003049;
}
.error {
  color: red;
}
</style>