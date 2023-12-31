<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"/>
    </div>
    <p v-if="savedCities.length === 0">
        No locations added. To start stracking a location, search in the field above.
    </p>
</template>

<script setup>
import axios from "axios";
import {ref} from "vue";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";

const savedCities =  ref([]);
const getCities = async () => {
    if ( localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(
            localStorage.getItem("savedCities")
        );

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&exclude={part}&appid=3a758b272aafc0830b14cdf2b0c09f58`)
            );
        });
        const weatherData = await Promise.all(requests);
        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }

};
await getCities();
const router = useRouter();
const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: {state: city.state, city: city.city},
        query: {id: city.id, lat: city.coords.lat , lng: city.coords.lng }
    });
};
</script>