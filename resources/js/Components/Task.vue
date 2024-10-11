<template>
    <div class="p-5 bg-white border-b border-gray-200">
        <!-- Input untuk pencarian -->
        <input
            type="text"
            v-model="searchTerm"
            placeholder="Cari tugas..."
            class="border border-gray-300 rounded p-2 mb-4 w-full"
        />

        <!-- Tombol untuk membuka modal -->
        <button
            @click="openModal"
            class="bg-green-500 text-white p-2 rounded mb-4"
        >
            Tambah Tugas
        </button>

        <!-- Menggunakan komponen TaskModal -->
        <TaskModal
            :task="task"
            :isOpen="isModalOpen"
            @submit="submitForm"
            @close="closeModal"
        />

        <!-- Menggunakan komponen ConfirmDelete -->
        <ConfirmDelete
            :task="taskToDelete"
            :isOpen="isConfirmDeleteOpen"
            @confirm="deleteTask(taskToDelete.id)"
            @close="closeConfirmDelete"
        />

        <!-- Notification Component -->
        <Notification
            :message="successMessage || errorMessage"
            :isOpen="!!successMessage || !!errorMessage"
            @close="successMessage = null; errorMessage = null"
            class="fixed bottom-5 right-5"
        />

        <h3 class="text-xl mb-2">Task List:</h3>
        <table class="w-full mb-4">
            <thead>
                <tr class="bg-gray-100 border-b border-gray-200">
                    <th class="px-4 py-2 text-left">Title</th>
                    <th class="px-4 py-2 text-left">Description</th>
                    <th class="px-4 py-2 text-right">Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(taskItem, index) in filteredTasks" :key="taskItem.id" class="border-b border-gray-200"> <!--ini di tambahin kondisi filteredTask-->
                    <td class="px-4 py-2">{{ taskItem.title }}</td>
                    <td class="px-4 py-2">{{ taskItem.description }}</td>
                    <td class="px-4 py-2 text-right">
                        <div class="flex justify-end">
                            <button
                                @click="editTask(taskItem)"
                                class="bg-yellow-500 text-white p-1 rounded mr-2"
                            >
                                Edit
                            </button>
                            <button
                                @click="openConfirmDelete(taskItem)"
                                class="bg-red-500 text-white p-1 rounded"
                            >
                                Hapus
                            </button>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from 'vue';
import axios from 'axios';
import TaskModal from './TaskModal.vue';
import ConfirmDelete from './ConfirmDelete.vue';
import Notification from './Notification.vue';

const tasks = ref([]);
const task = ref({ id: null, title: '', description: '' });
const taskToDelete = ref(null);
const isModalOpen = ref(false);
const isConfirmDeleteOpen = ref(false);
const errorMessage = ref(null);
const successMessage = ref(null);
const searchTerm = ref(''); // Ref untuk menyimpan kata kunci pencarian

const fetchTasks = async () => {
    try {
        const response = await axios.get('/api/tasks');
        console.log('Fetched Tasks:', response.data);
        tasks.value = response.data;
        errorMessage.value = null;
    } catch (error) {
        console.error('Error fetching tasks:', error);
        errorMessage.value = 'Gagal memuat tugas. Silakan coba lagi.';
    }
};

// Notif Edit Sama Simpen
const submitForm = async (submittedTask) => {
    try {
        if (submittedTask.id) {
            await axios.put(`/api/tasks/${submittedTask.id}`, submittedTask);
            successMessage.value = 'Tugas berhasil diperbarui!'; // Edit
        } else {
            await axios.post('/api/tasks', submittedTask);
            successMessage.value = 'Tugas berhasil ditambahkan!'; // Simpen
        }
        resetForm();
        closeModal();
        await fetchTasks();
        errorMessage.value = null;
    } catch (error) {
        errorMessage.value = 'Gagal menyimpan tugas. Silakan coba lagi.';
        successMessage.value = null;
    }
};

//Form untuk edit
const editTask = (taskToEdit) => {
    task.value = { ...taskToEdit };
    openModal();
};

// Modal Dialog Delete
const openConfirmDelete = (taskToItem) => {
    taskToDelete.value = taskToItem;
    isConfirmDeleteOpen.value = true;
};

// Ngapus Pake error Handling
const deleteTask = async (taskId) => {
    try {
        await axios.delete(`/api/tasks/${taskId}`);
        isConfirmDeleteOpen.value = false;
        await fetchTasks();
        successMessage.value = 'Tugas berhasil dihapus!';
        errorMessage.value = null;
    } catch (error) {
        errorMessage.value = 'Gagal menghapus tugas. Silakan coba lagi.';
        successMessage.value = null;
    }
};

// Gua gk tau ini buat apa, tapi harus ada
const resetForm = () => {
    task.value = { id: null, title: '', description: '' };
    taskToDelete.value = null;
};

// Buka dan tutup modal
const openModal = () => {
    isModalOpen.value = true;
};

const closeModal = () => {
    isModalOpen.value = false;
    resetForm();
};

// Buka dan tutup dialog konfirmasi
const closeConfirmDelete = () => {
    isConfirmDeleteOpen.value = false;
    taskToDelete.value = null;
};

// Ngambil Data Task dari Controller
onMounted(fetchTasks);

// Watcher buat ngilangin notifikasi
watch(successMessage, (newValue) => {
    if (newValue) {
        setTimeout(() => {
            successMessage.value = null;
        }, 3000); // 3 Detik
    }
});

// ini buat pencarian
const filteredTasks = computed(() => {
    if (!searchTerm.value) {
        return tasks.value; // buat nampilin hasil pencarian
    }
    //bagian ini gk bisa jelasin soalnya begitu dari sono nya
    const lowercasedSearchTerm = searchTerm.value.toLowerCase();
    return tasks.value.filter(taskItem =>
        taskItem.title.toLowerCase().includes(lowercasedSearchTerm) ||
        taskItem.description.toLowerCase().includes(lowercasedSearchTerm)
    );
});
</script>