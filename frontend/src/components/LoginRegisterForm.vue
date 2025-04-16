<script setup lang="ts">
    import { reactive, ref, defineProps } from 'vue';
    import api from '@/api';
    import { useRoute } from 'vue-router';
    import router from '@/router';
    import { ACCESS_TOKEN, REFRESH_TOKEN } from '@/constants';

    const route = useRoute();

    const props = defineProps<{
        method: 'login' | 'register';
    }>();

    const form = reactive({
        username: '',
        password: '',
        isLoading: false
    });

    const title = props.method === 'login' ? 'Login' : 'Register';
    const endpoint = props.method === 'login' ? '/api/token/' : '/api/user/register/';

    const handleSubmit = async () => {
        form.isLoading = true;

        const newUser = {
            username: form.username,
            password: form.password
        };

        try {
            const response = await api.post(endpoint, newUser);

            if (props.method === 'login') {
                localStorage.setItem(ACCESS_TOKEN, response.data.access);
                localStorage.setItem(REFRESH_TOKEN, response.data.refresh);
                router.push('/');
            } else {
                router.push('/login');
            }
        } catch (error) {
            console.error('Error:', error);
        } finally {
            form.isLoading = false;
        }
    };
</script>

<template>
    <form @submit.prevent="handleSubmit">
        <h2 class="text-3xl text-center font-semibold mb-6">{{ title }}</h2>
        <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2">
                Username
            </label>
            <input v-model="form.username"
                type="text"
                id="username"
                name="username"
                class="border rounded w-full py-2 px-3 mb-2"
                required/>
            <p></p>
            <label class="block text-gray-700 font-bold mb-2">
                Password
            </label>
            <input v-model="form.password"
                type="password"
                id="password"
                name="password"
                class="border rounded w-full py-2 px-3 mb-2"
                required/>
            <p></p>
            <button class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline" type="submit">
                {{ title }}
            </button>
        </div>
    </form>
</template>

<style src="../assets/Form.css"/>