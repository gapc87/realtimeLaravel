<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import {onMounted, onUnmounted, ref} from "vue";

const inputTxt = ref(null);
const messageText = ref('');
const onlineUsers = ref([]);
const messages = ref([]);
const chat = ref(null);

const sendMessage = () => {
    noApi.post('/chat', { message: messageText.value }).then(res => {
        console.log(res);
    }).finally(() => {
        messageText.value = '';
        inputTxt.value.focus();
        chat.value.scrollTop = chat.value.scrollHeight;
    });
}

onMounted(() => {
   Echo.join('chat')
       .here(users => onlineUsers.value = users)
       .joining(user => onlineUsers.value.push(user))
       .leaving(user => onlineUsers.value = onlineUsers.value.filter(u => u.id !== user.id))
       .listen('MessageSent', (e) => {
           messages.value.push(e);
       });
});

onUnmounted(() => {
    Echo.leave('chat');
})

</script>

<template>
    <AppLayout title="Chat">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Chat
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg p-6">
                    <div class="flex">
                        <div class="flex-col flex-1 mr-3">
                            <div ref="chat" class="rounded border border-gray-300 w-full h-96 p-1 overflow-y-scroll">
                                <ul v-for="message in messages">
                                    <li class="bg-gray-50 rounded p-1 pl-2 mt-1">
                                        <strong>{{ message.user.name }}:</strong> {{ message.message }}
                                    </li>
                                </ul>
                            </div>
                            <div class="flex mt-3">
                                <input
                                    @keyup.enter="sendMessage"
                                    ref="inputTxt"
                                    type="text"
                                    v-model="messageText"
                                    class="flex-1 mr-3 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                                >
                                <button
                                    @click="sendMessage"
                                    type="button"
                                    class="text-white bg-blue-600 hover:bg-blue-700 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm w-full sm:w-auto px-5 py-2.5 text-center"
                                >Send message</button>
                            </div>
                        </div>
                        <div class="bg-gray-50 rounded border border-gray-300 p-1 w-80">
                            <h5 class="font-bold text-center">Online Now</h5>
                            <ul class="mt-3">
                                <li v-for="onlineUser in onlineUsers">{{ onlineUser.name }}</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
