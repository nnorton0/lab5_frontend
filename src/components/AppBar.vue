<template>
  <header>
    <h1>Task List</h1>
    <div class="spacer"></div>
    <div v-if="!user"><a :href="loginUrl">Login</a></div>
    <swd-toggle-button v-else v-model:active="drawerOpen">&#x1F464;</swd-toggle-button>
    <swd-drawer v-model:open="drawerOpen">
      <div>User: {{ user?.email }}</div>
      <div><a :href="logoutUrl">Logout</a></div>
    </swd-drawer>
  </header>
</template>

<script setup>
import SwdToggleButton from './SwdToggleButton.vue';
import SwdDrawer from "./SwdDrawer.vue";
import { ref, onMounted } from 'vue';

// In Vue, .env values appear in the import.meta.env object.
const apiUrl = import.meta.env.VITE_API_URL;
const loginUrl = apiUrl + "/api/v1/auth/google?returnTo=" + window.location.href;
const logoutUrl = apiUrl + "/api/v1/auth/logout?returnTo=" + window.location.href;

// User must be a ref type so that when one component changes it, other components are notified.
// It defaults to null when the user has not logged in.
const user = ref(null);
const drawerOpen = ref(false);

async function getUser() {
  try {
    let response = await fetch(`${apiUrl}/api/v1/user`, { credentials: 'include' });
    if (!response.ok) {
      user.value = null;
      return;
    }
    user.value = await response.json();
  }
  catch (err) {
    console.error(err.message);
  }
}

// onMounted is called by Vue once the component has been loaded into the DOM
onMounted(getUser);
</script>

<style scoped>
header {
  display: flex;
  align-items: center;
}

h1 {
  font-size: 2em;
  font-weight: bold;
  color: darkblue;
}

div.spacer {
  flex-grow: 1;
}
</style>