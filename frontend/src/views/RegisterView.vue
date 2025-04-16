<script setup lang="ts">
    import LoginRegisterForm from '@/components/LoginRegisterForm.vue';

    localStorage.clear();
</script>

<template>
    <LoginRegisterForm method="register" />
</template>