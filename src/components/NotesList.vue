<template>
  <div class="notes-container">
    <h2>Notes from Backend</h2>
    <div v-if="loading" class="loading">Loading...</div>
    <div v-if="error" class="error">{{ error }}</div>
    <ul v-if="notes.length > 0" class="notes-list">
      <li v-for="note in notes" :key="note.id" class="note-item">
        <h3>{{ note.title }}</h3>
        <p>{{ note.body }}</p>
      </li>
    </ul>
    <div v-else-if="!loading && !error">
      No notes found. You can add some via the backend API.
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const API_URL = `${import.meta.env.VITE_API_BASE_URL}/api/notes`;

const notes = ref([]);
const loading = ref(true);
const error = ref(null);

async function fetchNotes() {
  try {
    const response = await fetch(API_URL);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    notes.value = await response.json();
  } catch (e) {
    error.value = `Failed to fetch notes: ${e.message}. Make sure the backend is running.`;
    console.error(e);
  } finally {
    loading.value = false;
  }
}

onMounted(() => {
  fetchNotes();
});
</script>

<style scoped>
.notes-container {
  max-width: 800px;
  margin: 2rem auto;
  padding: 1rem;
  font-family: sans-serif;
}
.loading, .error {
  text-align: center;
  padding: 1rem;
}
.error {
  color: red;
}
.notes-list {
  list-style: none;
  padding: 0;
}
.note-item {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 1rem;
  margin-bottom: 1rem;
}
.note-item h3 {
  margin-top: 0;
}
</style>