<template>
    <div class="flex py-3 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-xl font-bold">{{ city.city }}</h2>
            <p class="text-sm">{{ city.state }}</p>
        </div>
        <div class="flex flex-col gap-2">
            <p class="text-xl self-end font-bold">
                {{ Math.round(city.weather.main.temp)-273 }}&degC
            </p>
            <div class="flex gap-2">
                <span class="text-xs">
                    H:
                    {{ Math.round(city.weather.main.temp_max)-273 }}&degC
                </span>
                <span class="text-xs">
                    L:
                    {{ Math.round(city.weather.main.temp_min)-273 }}&degC
                </span>

            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    city:{
        type: Object,
        object: () => {},
    },
});
</script>