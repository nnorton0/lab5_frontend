<template>
  <div id="app">
    <AppBar />
    <main>
      <TaskForm @added="taskAdded" />
      <TaskList :update="update" />
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import AppBar from './components/AppBar.vue';
import TaskForm from './components/TaskForm.vue';
import TaskList from './components/TaskList.vue';

const update = ref(0);

function taskAdded() {
  update.value++;
}
</script>

<style>
main {
  padding: 1rem;
}
</style>