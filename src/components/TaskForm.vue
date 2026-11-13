<template>
  <div id="task-form">
    <form @submit.prevent="addTask">
      <div>
        <label for="task-text">Task Description:</label>
        <input id="task-text" type="text" v-model="text" class="task-form-input" required />
      </div>
      <div>
        <label for="task-date">Due Date:</label>
        <input id="task-date" type="date" v-model="date" class="task-form-input" required />
      </div>
      <button type="submit" class="btn-create-task">Add Task</button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['added']);
const text = ref('');
const date = ref('');
const apiUrl = import.meta.env.VITE_API_URL;

async function addTask() {
  if (!text.value || !date.value) return;

  try {
    let response = await fetch(`${apiUrl}/api/v1/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ text: text.value, date: date.value })
    });

    if (response.ok) {
      text.value = '';
      date.value = '';
      emit('added');
    }
  } catch (err) {
    console.error(err.message);
  }
}
</script>

<style scoped>
#task-form {
  margin-bottom: 1.5rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}
div {
  margin-bottom: 0.75rem;
}
label {
  display: block;
  font-weight: bold;
  margin-bottom: 0.25rem;
}
</style>