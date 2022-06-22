<script setup>
import UserRow from '@/Pages/Users/partials/UserRow.vue';
import {defineProps} from "vue";

let props = defineProps({
    users: Array
});

const emits = defineEmits(['deleteUser']);

const deleteUser = () => {
    emits('deleteUser');
};
</script>

<template>
    <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
        <tr>
            <th scope="col" class="px-6 py-3">
                Name
            </th>
            <th scope="col" class="px-6 py-3">
                Email
            </th>
            <th scope="col" class="px-6 py-3">
                <span class="sr-only">Edit</span>
            </th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="user in users" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
            <UserRow :user="user" @deleteUser="deleteUser" />
        </tr>
        </tbody>
    </table>
</template>
