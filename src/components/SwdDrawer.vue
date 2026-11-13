<template>
    <div class="holder" ref="holderRef">
        <div class="content" v-show="open"><slot /></div>
    </div>
</template>

<script setup>
import { ref, watch, onUnmounted } from 'vue';

const open = defineModel('open', {
    type: Boolean,
    default: false
});

const holderRef = ref(null);
let addListenerTimer = null;

function handleClickOutside(event) {
    if (holderRef.value && !holderRef.value.contains(event.target)) {
        open.value = false;
    }
}

function handleKeydown(event) {
    if (open.value && event.key === 'Escape') {
        open.value = false;
    }
}

// Only listen for outside clicks while open. The listener is registered
// via setTimeout (a macrotask), not nextTick (a microtask): the browser
// runs a microtask checkpoint after *each* listener in an event's bubble
// path, not just once at the end, so nextTick can still resolve before
// the same click event finishes bubbling to document. setTimeout(0) is
// guaranteed to run only after the whole click event (and its
// microtasks) has fully finished.
watch(open, (isOpen) => {
    clearTimeout(addListenerTimer);
    if (isOpen) {
        addListenerTimer = setTimeout(() => {
            document.addEventListener('click', handleClickOutside);
        }, 0);
    } else {
        document.removeEventListener('click', handleClickOutside);
    }
});

document.addEventListener('keydown', handleKeydown);

onUnmounted(() => {
    clearTimeout(addListenerTimer);
    document.removeEventListener('click', handleClickOutside);
    document.removeEventListener('keydown', handleKeydown);
});
</script>

<style scoped>
.holder {
    position: relative; /* Gives the reference that the child aligns to */
    display: inline-block; /* Required to give the element a vertical height for the .content element to reference. */
    min-height: 1lh;
}

.content {
    position: absolute;
    top: 120%; /* one line below the parent */
    right: 0; /* flush right with the parent. Change to left: 0; to make it flush left */
    width: max-content;
    max-width: 50vw;
    z-index: 1; /* on top of the default index of zero */
    border: 1px solid black;
    border-radius: 0.2em;
    background-color: white;
    color: black;
    padding: 0.2em;
    margin: 0px;
}
</style>