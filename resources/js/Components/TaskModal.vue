<template>
    <div
        v-if="isOpen"
        class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
    >
        <div class="bg-white p-4 rounded shadow-lg w-1/3">
            <h3 class="text-xl mb-2">{{ task.id ? 'Edit Tugas' : 'Tambah Tugas' }}</h3><!--Ini sama kaya IF jadi di buat ngeliat dia tambah atau edit-->
            <form @submit.prevent="submitForm">
                <input
                    type="text"
                    v-model="task.title"
                    placeholder="Judul Tugas"
                    required
                    class="border rounded p-2 w-full mb-2"
                />
                <textarea
                    v-model="task.description"
                    placeholder="Deskripsi Tugas"
                    class="border rounded p-2 w-full mb-2"
                ></textarea>
                <button
                    type="submit"
                    class="bg-blue-500 text-white p-2 rounded"
                >
                    Simpan
                </button>
                <button
                    type="button"
                    @click="closeModal"
                    class="bg-gray-500 text-white p-2 rounded ml-2"
                >
                    Tutup
                </button>
            </form>
        </div>
    </div>
</template>

<script setup>
import { ref, toRefs, defineProps, defineEmits } from 'vue';

const props = defineProps({
    task: {
        type: Object,
        default: () => ({ id: null, title: '', description: '' }),
    },
    isOpen: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits(['submit', 'close']);

const submitForm = () => {
    emit('submit', props.task);
};

const closeModal = () => {
    emit('close');
};
</script>
