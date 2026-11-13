<template>
  <div class="task-list-card">
    <h3>My Tasks</h3>
    <div v-if="tasks.length === 0" class="empty-state">
      No tasks found. Add a task above to get started!
    </div>
    <ul v-else class="task-list">
      <li v-for="task in tasks" :key="task._id || task.id" class="task-item">
        <div class="task-left">
          <input 
            type="checkbox" 
            :checked="task.done" 
            @change="toggleDone(task)" 
            class="task-checkbox"
          />
          <span :class="{ 'completed': task.done }" class="task-text">
            {{ task.title }}
          </span>
        </div>
        <div class="task-right">
          <span class="task-date">{{ task.dueDate }}</span>
          <button @click="deleteTask(task._id || task.id)" class="btn-delete" title="Delete Task">
            &times;
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const tasks = ref([]);
const apiUrl = import.meta.env.VITE_API_URL || "https://nnorton.4hx.net:1337";

async function fetchTasks() {
  try {
    const response = await fetch(`${apiUrl}/api/v1/tasks`, { 
      credentials: 'include' 
    });
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

<style scoped>
.task-list-card {
  background: #ffffff;
  padding: 1.5rem;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
  border: 1px solid #e2e8f0;
}
h3 {
  margin-top: 0;
  margin-bottom: 1rem;
  color: #1e293b;
  font-size: 1.2rem;
}
.empty-state {
  color: #64748b;
  text-align: center;
  padding: 1.5rem 0;
  font-style: italic;
}
.task-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.task-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0.85rem 0.5rem;
  border-bottom: 1px solid #f1f5f9;
}
.task-item:last-child {
  border-bottom: none;
}
.task-left {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  flex-grow: 1;
}
.task-checkbox {
  width: 1.2rem;
  height: 1.2rem;
  cursor: pointer;
}
.task-text {
  font-size: 1rem;
  color: #1e293b;
}
.completed {
  text-decoration: line-through;
  color: #94a3b8 !important;
}
.task-right {
  display: flex;
  align-items: center;
  gap: 1rem;
}
.task-date {
  font-size: 0.85rem;
  color: #64748b;
  background: #f8fafc;
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
  border: 1px solid #e2e8f0;
}
.btn-delete {
  background: transparent;
  border: none;
  color: #ef4444;
  font-size: 1.5rem;
  font-weight: bold;
  cursor: pointer;
  padding: 0;
  line-height: 1;
}
.btn-delete:hover {
  color: #b91c1c;
}
</style>