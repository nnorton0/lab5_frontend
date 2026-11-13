<template>
  <form @submit.prevent="submitTask" class="card p-4 shadow-sm mb-4 border-0">
    <h4 class="mb-3 fw-bold text-dark">Create Task</h4>
    <div class="mb-3">
      <input type="text" v-model="title" class="form-control" placeholder="Task description" required>
    </div>
    <div class="mb-3">
      <input type="date" v-model="dueDate" class="form-control" required>
    </div>
    <div>
      <button type="submit" class="btn btn-primary px-4">Create Task</button>
    </div>
  </form>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['task-added']);
const title = ref('');
const dueDate = ref('');
const apiUrl = import.meta.env.VITE_API_URL;

async function submitTask() {
  if (!title.value.trim() || !dueDate.value) return;

  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ title: title.value, dueDate: dueDate.value, done: false })
    });

    if (response.ok) {
      title.value = '';
      dueDate.value = '';
      emit('task-added');
    }
  } catch (err) {
    console.error('Error creating task:', err);
  }
}
</script>