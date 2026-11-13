<template>
  <div id="task-list">
    <v-list>
      <v-list-item v-for="task in tasks" :key="task._id">
        <template v-slot:prepend>
          <v-btn variant="text" @click="toggleTask(task)">
            <v-icon v-if="task.done" icon="mdi-checkbox-marked"></v-icon>
            <v-icon v-else icon="mdi-checkbox-blank-outline"></v-icon>
          </v-btn>
        </template>

        <v-list-item-title :class="{ 'task-completed': task.done }">
          {{ task.text }}
        </v-list-item-title>
        
        <v-list-item-subtitle>
          {{ formatDate(task.date) }}
        </v-list-item-subtitle>

        <template v-slot:append>
          <v-btn icon="mdi-trash-can" color="red" variant="text" @click="deleteTask(task._id)">
          </v-btn>
        </template>
      </v-list-item>
    </v-list>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';

const props = defineProps({
  update: {
    type: Number,
    default: 0
  }
});

const tasks = ref([]);
const apiUrl = import.meta.env.VITE_API_URL;

async function loadTasks() {
  try {
    let response = await fetch(`${apiUrl}/api/v1/tasks`, { credentials: 'include' });
    if (!response.ok) return;
    tasks.value = await response.json();
  } catch (err) {
    console.error(err.message);
  }
}

async function toggleTask(task) {
  try {
    const newState = !task.done;
    let response = await fetch(`${apiUrl}/api/v1/tasks/${task._id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ done: newState })
    });
    if (response.ok) {
      task.done = newState;
    }
  } catch (err) {
    console.error(err.message);
  }
}

async function deleteTask(id) {
  try {
    let response = await fetch(`${apiUrl}/api/v1/tasks/${id}`, {
      method: 'DELETE',
      credentials: 'include'
    });
    if (response.ok) {
      tasks.value = tasks.value.filter(t => t._id !== id);
    }
  } catch (err) {
    console.error(err.message);
  }
}

function formatDate(dateStr) {
  if (!dateStr) return '';
  return dateStr.substring(0, 10);
}

watch(() => props.update, () => {
  loadTasks();
});

onMounted(loadTasks);
</script>

<style scoped>
.task-completed {
  text-decoration: line-through;
  color: gray;
}
</style>