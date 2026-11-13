<template>
  <button type="button" class="toggle-btn" v-bind:aria-pressed="pressed ? 'true' : 'false'" @click="pressed = !pressed">
    <slot>{{ label }}</slot>
  </button>
</template>

<script setup>
const pressed = defineModel('active', {
  type: Boolean,
  default: false
});
</script>

<style scoped>
.toggle-btn {
  border: 0;
  padding: inherit;
  background: inherit;
  font: inherit;
  display: inline-block;
  cursor: pointer;
}

.toggle-btn[aria-pressed="true"] {
  background: #2563eb;
  color: white;
}

.toggle-btn:focus-visible {
  outline: 2px solid #2563eb;
  outline-offset: 2px;
}
</style>