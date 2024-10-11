<!-- Notification.vue -->
<template>
    <div v-if="isOpen" class="notification">
        <span class="message">{{ message }}</span>
        <span class="close" @click="$emit('close')">&times;</span>
    </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
    message: String,
    isOpen: Boolean
});
</script>
<!--Style dapet maling dari yang pinter-->
<style scoped>
.notification {
    position: fixed;
    bottom: 20px;
    right: 20px;
    background-color: #fff;
    border: 1px solid #ddd;
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
    transition: opacity 0.3s ease, transform 0.3s ease;
    opacity: 0.9;
    z-index: 1000;
}

.message {
    font-size: 14px;
    color: #333;
}

.close {
    position: absolute;
    top: 5px;
    right: 5px;
    font-size: 16px;
    cursor: pointer;
}
</style>
