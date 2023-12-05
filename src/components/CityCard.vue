<template>
    <div class="flex flex-row py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-3xl">{{ city.city }}</h2>
            <h3>{{ city.country }}</h3>
        </div>
        <div class="flex w-full  h-aut">
            <img class="w-[70px] self-center mx-auto"
                :src="`https://openweathermap.org/img/wn/${city.weather.weather[0].icon}@2x.png`" alt="icon weather">
        </div>
        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.main.temp) }}&deg;C
            </p>
            <div class="flex gap-2">
                <span class="text-xs">
                    <i class="fa-solid fa-angle-up" style="color: #f00000;"></i>
                    {{ Math.round(city.weather.main.temp_max) }}&deg;
                </span>
                <span class="text-xs">
                    <i class="fa-solid fa-angle-down" style="color: #002cf0;"></i>
                    {{ Math.round(city.weather.main.temp_min) }}&deg;
                </span>
            </div>
        </div>
    </div>
</template>

<script setup>
import { useRouter } from 'vue-router';

defineProps({
    city: {
        type: Object,
        default: () => { },
    }
})
</script>
