<template>
  <div class="card p-4 shadow-sm border-0">
    <h4 class="mb-3 fw-bold text-dark">My Tasks</h4>
    <div v-if="tasks.length === 0" class="text-muted fst-italic py-3">
      No tasks found. Add a task above to get started!
    </div>
    <ul class="list-group list-group-flush">
      <li v-for="task in tasks" :key="task._id || task.id" class="list-group-item d-flex justify-content-between align-items-center py-3 px-0">
        <div class="d-flex align-items-center gap-3">
          <button @click="toggleDone(task)" class="btn btn-link p-0 text-dark text-decoration-none d-flex align-items-center">
            <span class="material-icons fs-4 text-primary">{{ task.done ? 'check_box' : 'check_box_outline_blank' }}</span>
          </button>
          <span :class="{ 'text-decoration-line-through text-muted': task.done }" class="fs-6 fw-medium">
            {{ task.title }}
          </span>
        </div>
        <div class="d-flex align-items-center gap-3">
          <span class="badge bg-light text-secondary border px-2 py-1">{{ task.dueDate }}</span>
          <button @click="deleteTask(task._id || task.id)" class="btn btn-link p-0 text-danger d-flex align-items-center" title="Delete Task">
            <span class="material-icons fs-5">remove_circle</span>
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const tasks = ref([]);
const apiUrl = import.meta.env.VITE_API_URL;

async function fetchTasks() {
  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks`, { credentials: 'include' });
    if (response.ok) {
      tasks.value = await response.json();
    }
  } catch (err) {
    console.error('Failed to fetch tasks:', err);
  }
}

async function toggleDone(task) {
  const updatedStatus = !task.done;
  const taskId = task._id || task.id;
  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks/${taskId}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ ...task, done: updatedStatus })
    });
    if (response.ok) {
      task.done = updatedStatus;
    }
  } catch (err) {
    console.error('Failed to update task:', err);
  }
}

async function deleteTask(taskId) {
  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks/${taskId}`, {
      method: 'DELETE',
      credentials: 'include'
    });
    if (response.ok) {
      tasks.value = tasks.value.filter(t => (t._id || t.id) !== taskId);
    }
  } catch (err) {
    console.error('Failed to delete task:', err);
  }
}

onMounted(fetchTasks);
defineExpose({ fetchTasks });
</script>