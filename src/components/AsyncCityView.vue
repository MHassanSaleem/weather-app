<template>
    <div class="flex flex-col felx-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p> You are currently previewing this city, click the "+" icon to start tracking this city</p>
        </div>
        <!-- weather forecast -->
        <div class="flex flex-col items-center text-white py-12 w-full">
            <p class="text-4xl mb-2 flex">{{ route.params.city }} <p class="text-sm ml-3">{{ weatherData.city.country }}</p></p>
            <p class="text-sm">{{ (new Date(weatherData.list[0].dt*1000)).toLocaleString('en-US', { month: 'short', day: 'numeric' }) }}</p>
            <img :src="`http://openweathermap.org/img/wn/${weatherData.list[0].weather[0].icon}.png`"/>
            <h1 class="text-4xl mb-2 font-extrabold">{{ Math.round(weatherData.list[0].main.temp - 273.15)  }}°C</h1>
            <div class="mt-3 border-b-2 border-weather-secondary w-full"></div>
            <p class="mt-8 mb-1 text-light"> Forecast <i class="fa-solid fa-arrow-down text-sm"></i></p>
            <div v-if="weatherData.list.length > 0" class="w-3/4">
                <div v-for="forecast in weatherData.list" :key="forecast.dt" class="py-1/2 px-2 border-b-2 border-weather-secondary flex justify-between">
                    <p class="text-sm self-center">{{ (new Date(forecast.dt*1000)).toLocaleString('en-US', { month: 'short', day: 'numeric' }) }}</p>
                    <p class="text-sm self-center">{{ (new Date(forecast.dt*1000)).toLocaleString('en-US', { hour: 'numeric', minute: 'numeric' }) }}</p>
                    <img :src="`http://openweathermap.org/img/wn/${forecast.weather[0].icon}.png`"/>
                    <h2 class="text-2xl mt-3">{{ Math.round(forecast.main.temp - 273.15)}}°C</h2>
                    <div class="ml-15 self-center">
                        <p class="text-xs font-light">{{ forecast.weather[0].description }}</p>
                        <p class="text-xs font-light">Wind: {{ forecast.wind.speed }} m/s</p>
                    </div>
                </div>
            </div>
        </div>
        <!-- Remove city -->
        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const getWeatherData = async () => {
 try {
    const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=3a758b272aafc0830b14cdf2b0c09f58` );
    /* handling time difference here
    //calculating local time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.list.dt * 1000 + localOffset;
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset; 
    // hourly weather
    weatherData.data.dt.forEach((dt) => {
        const utc = dt * 1000 + localOffset;
        data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });*/ 

    //watermark dely
    await new Promise((res) => setTimeout(res, 1000));

    return weatherData.data;
 } catch (error) {
    console.log(error);
 }
};

const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = ( ) => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem("savedCities",  JSON.stringify(updatedCities));
    router.push({
        name: "home",
    });
};

</script>

 