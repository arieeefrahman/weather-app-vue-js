<template>
    <div class="flex px-4 py-6 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-2xl font-bold"> {{ city.city }}</h2>
            <h3>{{ city.state }}</h3>
        </div>

        <div class="flex flex-col gap-2">
            <p class="text-2xl self-end"> {{ Math.round(city.weather.main.temp) }}&deg;</p>
            <div class="flex gap-2">
                <span class="text-xs">
                    H: {{ Math.round(city.weather.main.temp_max) }}
                </span>
                <span class="text-xs">
                    L: {{ Math.round(city.weather.main.temp_min) }}
                </span>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        type: Object,
        default: () => {},
    }
});
</script>