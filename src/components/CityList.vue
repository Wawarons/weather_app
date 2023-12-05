<template>
    <div v-for="city in savedCities" :key="city.id" @click="goToCityView(city)">
        <CityCard :city="city"/>
    </div>
    <p v-if="savedCities.length === 0">
        No location added. To start tracking a location, search in the field above.
    </p>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import CityCard from './CityCard.vue';

const savedCities = ref([]);
const router = useRouter();

const getSavedCities = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }
    const requests = [];
    savedCities.value.forEach((city) => {
        requests.push(axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.long}&appid=${import.meta.env.VITE_WEATHER_API_KEY}&units=metric`))
    });

    const weatherData = await Promise.all(requests);

    weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value.data;
    })
}

const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: {
            country: city.country,
            city: city.city
        },
        query: {
            id: city.id,
            lat: city.coords.lat,
            long: city.coords.long
        }
    })


}

await getSavedCities();

</script>