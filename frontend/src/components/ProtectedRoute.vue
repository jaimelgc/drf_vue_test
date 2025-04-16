<script setup lang="ts">
    import { jwtDecode } from 'jwt-decode';
    import { useRouter, useRoute } from 'vue-router';
    import { ref, onMounted, watch } from 'vue';
    import api from '@/api';
    import { REFRESH_TOKEN, ACCESS_TOKEN } from '@/constants';
    

    const router = useRouter();
    const isAuthorized = ref<boolean | null>(null);

    const refreshToken = async () => {
        const refreshToken = localStorage.getItem(REFRESH_TOKEN);
        try {
            const res = await api.post('/api/token/refresh/', {
                refresh: refreshToken,
            });
            if (res.status === 200) {
                localStorage.setItem(ACCESS_TOKEN, res.data.access);
                isAuthorized.value = true;
            } else {
                isAuthorized.value = false;
            }
        } catch (error) {
            console.error(error);
            isAuthorized.value = false;
        }
    };

    const auth = async () => {
        const token = localStorage.getItem(ACCESS_TOKEN);
        if (!token) {
            isAuthorized.value = false;
            return;
        }

        try {
            const decoded: { exp: number } = jwtDecode(token);
            const now = Date.now() / 1000;

            if (decoded.exp < now) {
            await refreshToken();
            } else {
            isAuthorized.value = true;
            }
        } catch (error) {
            console.error(error);
            isAuthorized.value = false;
        }
    };

    onMounted(() => {
        auth();
    });

    watch(isAuthorized, (val) => {
        if (val === false) {
            router.push('/login');
        }
    });

</script>

<template>
  <div v-if="isAuthorized === null">Loading...</div>
  <slot v-else-if="isAuthorized" />
  <div v-else />
</template>