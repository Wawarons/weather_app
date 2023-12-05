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
          new Date(weatherData.currentTime).toLocaleDateString("en-us",
              {
                weekday: "short",
                day: "2-digit",
                month: "long"
              }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
            timeStyle: "short"
          })
        }}
      </p>
      <p class="text-8xl mb-8 rounded-full">{{ Math.round(weatherData.main.temp) }}&deg;C</p>
      <div class="flex flex-row mb-8 rounded-full items-center bg-red">
        <div class="px-2 text-center bg-weather-secondary rounded mx-1.5 shadow-2xl">
          <p class="text-2xl text-center">{{ Math.round(weatherData.main.temp_min) }}&deg;</p>
          <p>Min</p>
        </div>
        <div class="px-2 text-center bg-weather-secondary rounded mx-1.5 shadow-2xl">
          <p class="text-2xl text-center">{{ Math.round(weatherData.main.temp_max) }}&deg;</p>
          <p>Max</p>
        </div>
      </div>
      <p>Feels like {{ Math.round(weatherData.main.feels_like) }}&deg;C</p>
      <p class="capitalize">{{ weatherData.weather[0].description }}</p>
      <img class="w-[150px] h-auto" :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
           alt="icon weather">
      <div class="flex flex-row flex-1 text-center">
        <div class="p-2">
          <i class="fa-solid fa-wind" title="wind"></i>
          <p>{{Math.round(weatherData.wind.speed)}} m/s</p>
        </div>
        <div class="p-2">
          <i class="fa-solid fa-droplet" title="humidity"></i>
          <p>{{weatherData.main.humidity}}%</p>
        </div>

      </div>
    </div>
    <!--Hourly weather-->
    <hr class="border-white border-opacity-10 border w-full">
    <h2 class="py-2 text-white text-4xl">Hourly Forecast</h2>
    <div
        class="flex flex-row flex-1 border-2 border-white rounded-2xl my-10 text-white shadow-2xl bg-weather-secondary">
      <div v-for="(hourly, index) in forecastData.list" :key="index">
        <div v-if="index < 3" :class="{ 'border-r-2': index !== 2 }" class="items-center p-2">
          <img class="w-[75px] h-auto" :src="`https://openweathermap.org/img/wn/${hourly.weather[0].icon}@2x.png`"
               alt="icon weather">
          <p class="text-center">{{
              new Date(hourly.currentTime).toLocaleTimeString("en-us", {
                hour: "numeric"
                  }
              )
            }}</p>
        </div>
      </div>
    </div>
    <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" v-if="!route.query.preview" @click="removeCity">
      <p>Remove City</p>
      <i class="fa-solid fa-trash"></i>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import {useRoute, useRouter} from "vue-router";

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

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};

const getForecastData = async () => {
  try {
    const forecastData = await axios.get(`https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.long}&appid=${import.meta.env.VITE_WEATHER_API_KEY}&units=metric`);

    // cal hourly weather offset
    const localOffset = new Date().getTimezoneOffset() * 60000;
    forecastData.data.list.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime =
          utc + 1000 * forecastData.data.city.timezone;
    });
    return forecastData.data;
  } catch (err) {
    console.error(err);
  }
}

const router = useRouter();

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem('savedCities'));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem('savedCities', JSON.stringify(updatedCities));
  router.push({
    name: 'home',
  })
}

const weatherData = await getWeatherData();
const forecastData = await getForecastData();
</script>