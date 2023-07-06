<template>
    <div class="flex w-full justify-center align-middle items-center">
        <Suspense>
            <AsyncCityView class="mb-auto" />

            <template #fallback class="flex w-full h-screen justify-center items-center align-middle">
                <div class="flex items-center justify-center h-full">
                    <div class="animate-spin rounded-full border-t-4 border-b-4 border-blue-500 h-12 w-12"></div>
                </div>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';


</script>