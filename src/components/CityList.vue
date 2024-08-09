<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="toCityView(city)"/>
    </div>

    <p v-if="savedCities.length === 0">
        No locations saved. Search in the field above to start tracking a location.
    </p>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';

const openWeatherAPIKey = import.meta.env.VITE_OPEN_WEATHER_API_KEY;

const savedCities = ref([]);
const getCitiesList = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coordinates.lat}&lon=${city.coordinates.lng}&units=imperial&appid=${openWeatherAPIKey}`))
        });

        const weatherData = await Promise.all(requests);

        // flicker delay
        await new Promise((res) => setTimeout(res, 3000));

        weatherData.forEach((val, idx) => {
            savedCities.value[idx].weather = val.data;
        });
    };
};

await getCitiesList();


const router = useRouter();
const toCityView = (city) => {
    router.push({
        name: "cityView",
        params: { 
            state: city.state, 
            city: city.city 
        },
        query: { 
            id: city.id, 
            lat: city.coordinates.lat, 
            lng: city.coordinates.lng 
        },
    });
};
</script>