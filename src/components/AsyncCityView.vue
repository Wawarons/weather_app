<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- banner -->
    <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
      <p>
        You are currently previewing this city, click the "+" icon to start tracking this city.
      </p>
    </div>
    <!-- Weather overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("fr-fr",
              {
                weekday: "short",
                day: "2-digit",
                month: "long"
              }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("fr-fr", {
            timeStyle: "short"
          })
        }}
      </p>
      <p class="text-8xl mb-8 rounded-full">{{ Math.round(weatherData.main.temp) }}&deg;C</p>
        <p>Feels like {{ Math.round(weatherData.main.feels_like) }}&deg;C</p>
        <p class="capitalize">{{ weatherData.weather[0].description }}</p>
        <img class="w-[150px] h-auto" :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
             alt="icon weather">
    </div>
    <hr class="border-white border-opacity-10 border w-full">
  </div>
</template>

<script setup>
import axios from "axios";
import {useRoute} from "vue-router";

const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.long}&appid=${import.meta.env.VITE_WEATHER_API_KEY}&units=metric`
    );
    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.dt * 1000 + localOffset;
    weatherData.data.currentTime =
        utc + 1000 * weatherData.data.timezone;
    console.log(weatherData.data);

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();
</script>