<template>
    <div>
        <Suspense>
            <AsyncCityView></AsyncCityView>
            <template #fallback>
            <!-- <p>Loading...</p> -->
            <CityViewSkeleton />
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import CityViewSkeleton from '../components/CityViewSkeleton.vue';
import AsyncCityView from './AsyncCityView.vue';


</script>

