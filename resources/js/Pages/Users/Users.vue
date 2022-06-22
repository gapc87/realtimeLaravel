<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import UsersList from '@/Pages/Users/partials/UsersList.vue';
import Modal from '@/Jetstream/Modal';
import {onMounted, ref} from "vue";
import {Inertia} from "@inertiajs/inertia";
import {useToast, TYPE} from "vue-toastification";
import {useForm} from "@inertiajs/inertia-vue3";

defineProps({
    users: Array,
});

const emit = defineEmits(['closeModal']);

const createNewUserModal = ref(false);

const toast = useToast();

const deletedOrCreatedUser = () => {
    Inertia.reload({ only: ['users'], preserveScroll: true });
};

const createForm = useForm({
    name: '',
    email: '',
    password: '',
});

const createUser = ()  => {
    api.post(`/users`, createForm.data()).then(res => {
        if (res.status === 200) {
            cancelCreateUser();
        }
    }).finally(() => deletedOrCreatedUser());
};

const cancelCreateUser = () => {
    createForm.reset();
    createNewUserModal.value = false;
};

onMounted(() => {
    Echo.channel('users')
        .listen('UserCreated', (e) => {
                toast('The user with name ' + e.user.name + ' successfully created.', { type: TYPE.SUCCESS });
                deletedOrCreatedUser();
            })
        .listen('UserUpdated', (e) => {
                toast('The user with name ' + e.user.name + ' successfully updated.', { type: TYPE.INFO });
                deletedOrCreatedUser();
            })
        .listen('UserDeleted', (e) => {
                toast('The user with name ' + e.user.name + ' successfully deleted.', { type: TYPE.ERROR });
                deletedOrCreatedUser();
            });
});
</script>

<template>
    <AppLayout title="Users">
        <template #header>
            <div class="flex justify-between">
                <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                    Users
                </h2>
                <button
                    @click="createNewUserModal = !createNewUserModal"
                    type="button"
                    class="text-white bg-blue-600 hover:bg-blue-700 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm w-full sm:w-auto px-5 py-2.5 text-center"
                >
                    New user
                </button>
            </div>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <UsersList :users="users" @deleteUser="deletedOrCreatedUser" />
                </div>
            </div>
        </div>
    </AppLayout>

    <Modal :show="createNewUserModal">
        <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class="sm:flex sm:items-start">
                <div class="w-full mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
                    <h3 class="text-lg">
                        New User
                    </h3>

                    <div class="mt-2">
                        <form @submit.prevent="createUser">
                            <div>
                                <label for="name">Name</label>
                                <input
                                    id="name"
                                    v-model="createForm.name"
                                    type="text"
                                    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                                    required
                                    autofocus
                                />
                            </div>
                            <div class="mt-4">
                                <label for="email">Email</label>
                                <input
                                    id="email"
                                    v-model="createForm.email"
                                    type="email"
                                    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                                    required
                                />
                            </div>
                            <div class="mt-4">
                                <label for="password">Password</label>
                                <input
                                    id="password"
                                    v-model="createForm.password"
                                    type="password"
                                    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                                    required
                                />
                            </div>

                            <div class="flex items-center justify-end mt-4">
                                <button type="button" class="font-medium text-red-600 hover:underline" @click="cancelCreateUser">Cancel</button>
                                <button type="submit" class="font-medium text-green-600 hover:underline ml-3" :class="{ 'opacity-25': createForm.processing }" :disabled="createForm.processing">
                                    Create
                                </button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </Modal>
</template>
