<template>
    <Suspense>
        <async-city-view />
        <template #fallback >
            <p>Loading...</p>
        </template>
    </Suspense>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue'
</script>

<style lang="scss" scoped></style>