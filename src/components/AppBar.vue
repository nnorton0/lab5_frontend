<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark px-4 py-2 border-bottom shadow-sm">
    <a href="#" class="navbar-brand fw-bold">BYU ITC-210 Tasks</a>
    <div class="ms-auto d-flex align-items-center gap-3">
      <div v-if="!user">
        <a :href="loginUrl" class="btn btn-outline-light btn-sm">Login with Google</a>
      </div>
      <div v-else class="d-flex align-items-center gap-3 text-white">
        <div class="d-flex align-items-center gap-2">
          <img v-if="user.picture" :src="user.picture" alt="Profile" class="rounded-circle border border-light" width="32" height="32" />
          <span class="material-icons" v-else>account_circle</span>
          <span>Welcome, <strong>{{ user.name || user.email }}</strong></span>
        </div>
        <a :href="logoutUrl" class="btn btn-outline-light btn-sm">Log Out</a>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const loginUrl = import.meta.env.VITE_API_URL + "/api/v1/auth/google?returnTo=" + window.location.href;
const logoutUrl = import.meta.env.VITE_API_URL + "/api/v1/auth/logout?returnTo=" + window.location.href;
const user = ref(null);

async function getUser() {
  try {
    const apiUrl = import.meta.env.VITE_API_URL;
    if (!apiUrl) return;
    let response = await fetch(`${apiUrl}/api/v1/user`, { credentials: 'include' });
    if (!response.ok) {
      user.value = null;
      return;
    }
    user.value = await response.json();
  } catch (err) {
    console.error(err.message);
  }
}

onMounted(getUser);
</script>