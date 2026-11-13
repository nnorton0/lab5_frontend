<template>
  <header class="app-bar">
    <div class="brand">
      <h1>Task List</h1>
    </div>
    <div class="spacer"></div>
    <div v-if="!user" class="auth-action">
      <a :href="loginUrl" class="btn btn-login">Login with Google</a>
    </div>
    <div v-else class="user-menu">
      <div class="user-info">
        <img 
          v-if="userPicture" 
          :src="userPicture" 
          alt="Profile" 
          class="avatar" 
          @error="handleImageError"
        />
        <div v-else class="avatar-fallback">&#x1F464;</div>
        <span class="user-greeting">Welcome, <strong>{{ user.name || user.email }}</strong></span>
      </div>
      <a :href="logoutUrl" class="btn btn-logout">Logout</a>
    </div>
  </header>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const apiUrl = import.meta.env.VITE_API_URL || "https://nnorton.4hx.net:1337";
const loginUrl = `${apiUrl}/api/v1/auth/google?returnTo=` + window.location.href;
const logoutUrl = `${apiUrl}/api/v1/auth/logout?returnTo=` + window.location.href;

const user = ref(null);
const imageFailed = ref(false);

const userPicture = computed(() => {
  if (imageFailed.value || !user.value) return null;
  return user.value.picture || user.value.avatar || user.value.photo || user.value.imageUrl || null;
});

function handleImageError() {
  imageFailed.value = true;
}

async function getUser() {
  try {
    let response = await fetch(`${apiUrl}/api/v1/user`, { credentials: 'include' });
    if (!response.ok) {
      user.value = null;
      return;
    }
    user.value = await response.json();
  } catch (err) {
    console.error("Failed to fetch user:", err.message);
  }
}

onMounted(getUser);
</script>

<style scoped>
.app-bar {
  display: flex;
  align-items: center;
  padding: 1rem 2rem;
  background: #ffffff;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  border-bottom: 1px solid #eaeaea;
}
h1 {
  font-size: 1.5rem;
  font-weight: 700;
  color: #1e293b;
  margin: 0;
}
.spacer {
  flex-grow: 1;
}
.user-menu {
  display: flex;
  align-items: center;
  gap: 1.25rem;
}
.user-info {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  font-size: 0.95rem;
  color: #334155;
}
.avatar {
  width: 38px;
  height: 38px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid #3b82f6;
}
.avatar-fallback {
  width: 38px;
  height: 38px;
  border-radius: 50%;
  background: #e2e8f0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.1rem;
}
.btn {
  padding: 0.5rem 1rem;
  border-radius: 6px;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.9rem;
  cursor: pointer;
  transition: background 0.2s;
}
.btn-login {
  background: #2563eb;
  color: white;
}
.btn-login:hover {
  background: #1d4ed8;
}
.btn-logout {
  background: #f1f5f9;
  color: #ef4444;
  border: 1px solid #e2e8f0;
}
.btn-logout:hover {
  background: #fee2e2;
}
</style>