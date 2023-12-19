<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input 
      type="text" 
      v-model="searchQuery" 
      @input = "getSearchResults"
      placeholder="Search for a city or state" 
      class="py-2 px-1 w-full bg-transparent border-b
      focus:border-weather-secondary focus:outline-none 
      focus:shadow-[0px_1px_0_0_#004E71]"/>
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <li v-for="searchResult in mapboxSearchResults"
        :key="searchResult.id"
        class="py-2 cursor-pointer">
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from "axios";

const mapboxAPIKey= "pk.eyJ1IjoiaGFzYW5zYWxpbTc0OSIsImEiOiJjbHFja3dlZmkwM2d2MmtvNnA4MmRweWl5In0.6z45gWsOyVVDNORD27xvHg"
;const searchQuery = ref("");
const queryTimeOut= ref(null);
const mapboxSearchResults = ref(null);

const getSearchResults = async () => {
  clearTimeout(queryTimeOut.value);

  queryTimeOut.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );

        if (result.data && result.data.features) {
          mapboxSearchResults.value = result.data.features;
        } else {
          console.error("Unexpected API response:", result.data);
        }
      } catch (error) {
        console.error("Error fetching data from the API:", error);
      }
    } else {
      mapboxSearchResults.value = null;
    }
  }, 300);
};

</script>
