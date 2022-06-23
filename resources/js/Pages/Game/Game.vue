<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import {onMounted, ref} from "vue";

const circle = ref(null);
const winner = ref(null);
const result = ref('');
const timer = ref('Remaining time');
const bet = ref(null);

onMounted(() => {
    Echo.channel('game')
        .listen('RemainingTimeChanged', (e) => {
            timer.value = e.time;
            circle.value.classList.add('refresh');
            winner.value = null;
            result.value = '';
        })
        .listen('WinnerNumberGenerated', (e) => {
            circle.value.classList.remove('refresh');
            winner.value = e.number;

            if (winner.value === bet.value) {
                result.value = 'You WIN';
            } else {
                result.value = 'You lose';
            }
        })
})
</script>

<template>
    <AppLayout title="Users">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Game
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <div class="mx-3 my-3 flex-col text-center">
                        <img ref="circle" class="mx-auto" src="/img/circle.png" alt="Circle" height="250" width="250">
                        <p class="text-3xl text-sky-600 mt-3">{{ winner }}</p>
                        <hr class="my-3">
                        <div>
                            <label for="bet">Your bet</label>
                            <select v-model="bet" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5">
                                <option selected>Not in</option>
                                <option v-for="i in 12" :value="i">{{ i }}</option>
                            </select>
                        </div>
                        <hr class="my-3">
                        <p class="text-2xl">Remaining time</p>
                        <p class="text-red-500">{{ timer }}</p>
                        <hr class="my-3">
                        <p class="text-2xl" :class="result === 'You WIN' ? 'text-green-500' : 'text-red-500'">{{ result }}</p>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>

<style scoped>
@keyframes rotate {
    from {
        transform: rotate(0deg);
    }
    to {
        transform: rotate(360deg);
    }
}

.refresh {
    animation: rotate 1.5s linear infinite;
}
</style>
