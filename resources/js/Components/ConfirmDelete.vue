<template>
    <div
        v-if="isOpen"
        class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
    >
        <div class="bg-white p-4 rounded shadow-lg w-1/3">
            <h3 class="text-lg font-bold mb-2">Konfirmasi Hapus</h3>
            <p>Apakah Anda yakin ingin menghapus tugas "{{ task.title }}"?</p>
            <div class="mt-4">
                <button
                    @click="confirmDelete"
                    class="bg-red-500 text-white p-2 rounded"
                >
                    Hapus
                </button>
                <button
                    @click="closeDialog"
                    class="bg-gray-500 text-white p-2 rounded ml-2"
                >
                    Batal
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
    task: {
        type: Object,
        default: () => ({ title: '' }),
    },
    isOpen: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits(['confirm', 'close']);

const confirmDelete = () => {
    emit('confirm', props.task.id);
};

const closeDialog = () => {
    emit('close');
};
</script>