<template>
  <div id="app" class="app-layout">
    <app-bar></app-bar>
    <main class="main-content">
      <div class="container">
        <task-form @task-added="refreshTasks"></task-form>
        <task-list ref="taskListRef"></task-list>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import AppBar from '@/components/AppBar.vue';
import TaskForm from '@/components/TaskForm.vue';
import TaskList from '@/components/TaskList.vue';

const taskListRef = ref(null);

function refreshTasks() {
  if (taskListRef.value) {
    taskListRef.value.fetchTasks();
  }
}
</script>

<style>
body {
  margin: 0;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  background-color: #f8fafc;
  color: #0f172a;
}
.app-layout {
  min-height: 100vh;
}
.main-content {
  padding: 2.5rem 1rem;
}
.container {
  max-width: 680px;
  margin: 0 auto;
}
</style>