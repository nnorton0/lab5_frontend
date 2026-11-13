<template>
  <div id="app" class="min-vh-100 bg-light">
    <app-bar></app-bar>
    <main class="container py-4" style="max-width: 720px;">
      <task-form @task-added="refreshTasks"></task-form>
      <task-list ref="taskListRef"></task-list>
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