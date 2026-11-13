<template>
  <div class="task-form-card">
    <h3>Create Task</h3>
    <form @submit.prevent="submitTask">
      <div class="form-group">
        <input 
          type="text" 
          v-model="title" 
          placeholder="Task description" 
          required 
        />
      </div>
      <div class="form-group">
        <input 
          type="date" 
          v-model="dueDate" 
          required 
        />
      </div>
      <button type="submit" class="btn-submit">Create Task</button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['task-added']);
const title = ref('');
const dueDate = ref('');
const apiUrl = import.meta.env.VITE_API_URL || "https://nnorton.4hx.net:1337";

async function submitTask() {
  if (!title.value.trim() || !dueDate.value) return;

  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ 
        title: title.value.trim(), 
        dueDate: dueDate.value, 
        done: false 
      })
    });

    if (response.ok) {
      title.value = '';
      dueDate.value = '';
      emit('task-added');
    } else {
      console.error('Server returned error on task creation');
    }
  } catch (err) {
    console.error('Error submitting task:', err);
  }
}
</script>

<style scoped>
.task-form-card {
  background: #ffffff;
  padding: 1.5rem;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
  margin-bottom: 1.5rem;
  border: 1px solid #e2e8f0;
}
h3 {
  margin-top: 0;
  margin-bottom: 1rem;
  color: #1e293b;
  font-size: 1.2rem;
}
.form-group {
  margin-bottom: 1rem;
}
input[type="text"],
input[type="date"] {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  font-size: 1rem;
  box-sizing: border-box;
}
input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}
.btn-submit {
  background: #2563eb;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  width: 100%;
  transition: background 0.2s;
}
.btn-submit:hover {
  background: #1d4ed8;
}
</style>