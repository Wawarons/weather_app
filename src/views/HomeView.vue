<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
          type="text"
          v-model="searchQuery"
          @input="getSearchRequest"
          name="" id="" placeholder="search for a city or state" class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]
">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px] rounded"
          v-if="citySearchResults">
        <li v-if="searchError">Sorry, something went wrong, please try again</li>
        <li v-if="!searchError && citySearchResults.length === 0">No result found</li>
        <template v-else>
          <li v-for="city in citySearchResults" :key="city.id" class="py-2 px-4 cursor-pointer">
            {{ city.name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'

const citySearchResults = ref(null);
const queryTimeout = ref(null);
const searchError = ref(null)
const getSearchRequest = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
        await axios.get(`https://api.api-ninjas.com/v1/city?name=${searchQuery.value}&limit=5`,{
        method: 'GET',
        headers: {'X-Api-Key': import.meta.env.VITE_API_KEY},
        contentType: 'application/json'
      }).then((response) => {
        if (response)
          citySearchResults.value = response.data
        console.log(citySearchResults.value);
      }).catch(err => {
        console.error(err);
        searchError.value = true;
      });
    } else {
      citySearchResults.value = null;
    }
  }, 300)
}

const searchQuery = ref("")
</script>