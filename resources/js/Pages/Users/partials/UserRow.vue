<script setup>
import {defineProps} from "vue";
import {ref} from "vue";

const props = defineProps({
    user: Object
});

const edit = ref(false);

const emits = defineEmits(['deleteUser']);

const save = () => {
    const { id, name, email } = props.user;

    api.put(`/users/${id}`, {
        name,
        email
    }).then(res => {
        if (res.status === 200) {
            edit.value = false;
        }
    });
}

const deleteUser = () => {
    const { id } = props.user;

    api.delete(`/users/${id}`)
        .then(res => {
            if (res.status === 200) {
                emits('deleteUser');
            }
        })
}
</script>

<template>
    <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
        <template v-if="edit">
            <input
                type="text"
                class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                :placeholder="user.name"
                v-model="user.name"
                required
            >
        </template>
        <template v-else>
            {{ user.name }}
        </template>
    </th>
    <td class="px-6 py-4">
        <template v-if="edit">
            <input
                type="email"
                class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full"
                :placeholder="user.email"
                v-model="user.email"
                required
            >
        </template>
        <template v-else>
            {{ user.email }}
        </template>
    </td>
    <td class="px-6 py-4 text-right">
        <template v-if="edit">
            <button
                type="button"
                class="font-medium text-blue-600 hover:underline mr-3"
                @click="save"
            >
                Save
            </button>
            <button
                type="button"
                class="font-medium text-red-600 hover:underline"
                @click="edit = !edit"
            >
                Cancel
            </button>
        </template>
        <template v-else>
            <button
                type="button"
                class="font-medium text-blue-600 dark:text-blue-500 hover:underline mr-3"
                @click="edit = !edit"
            >
                Edit
            </button>
            <button @click="deleteUser" type="button" class="font-medium text-red-600 dark:text-red-500 hover:underline">Delete</button>
        </template>
    </td>
</template>
