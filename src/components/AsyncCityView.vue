<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
        </div>
        <!-- Weather Overview -->
         <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-5xl mb-2 font-bold"> {{ route.params.city.replace(/_/g, " ") }}</h1>
            <p class="text-base mb-12">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString(
                        "en-us",
                        {   
                            timeStyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-7xl mb-8">
                {{ Math.round((weatherData.current.temp - 32) * 5 / 9) }}&deg;C
            </p>
            <p>
                Feels like {{ Math.round((weatherData.current.feels_like - 32) * 5 / 9) }}&deg;C
            </p>
            <p class="capitalize">
                {{ weatherData.current.weather[0].description }}
            </p>
            <img :src="`https://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt="" class="w-[150px] h-auto">
         </div>
         <hr class="border-white border-opacity-10 border w-full" />

         <!-- Hourly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4 text-xl font-bold uppercase">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.hourly" :key="hourData.dt" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-base">
                            {{ new Date(hourData.currentTime).toLocaleTimeString("en-us", { hour: 'numeric'}) }}
                        </p>
                        <img :src="`https://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" alt="" class="w-auto h-[50px] object-cover">
                        <p class="text-xl">{{ Math.round((hourData.temp - 32) * 5 / 9) }}&deg;C</p>
                    </div>
                </div>
            </div>
        </div>
        <hr class="border-white border-opacity-10 border w-full" />

        <!-- Weekly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4 text-xl font-bold uppercase">7-Day Forecast</h2>
                <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center">
                    <p class="flex-1">{{ new Date(day.dt * 1000).toLocaleDateString("en-us", { weekday: 'long'}) }}</p>
                    <img :src="`https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt="" class="w-[50px] h-[50px] object-cover">
                    <div class="flex gap-2 flex-1 justify-end">
                        <p>H: {{ Math.round(day.temp.max) }}</p>
                        <p>L: {{ Math.round(day.temp.min) }}</p>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="!route.query.preview" class="flex items-center gap-2 my-12 p-4 text-white cursor-pointer duration-150 bg-red-600 hover:bg-red-700 rounded-md" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
        
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const openWeatherAPIKey = import.meta.env.VITE_OPEN_WEATHER_API_KEY;
const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&units=imperial&appid=${openWeatherAPIKey}`);

        // calculate current date and time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;

        // calculate hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;

        });

        return weatherData.data;
    } catch (error) {
        console.log(error)
    }
}

const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));
    router.push({
        name: 'home',
    });
};
</script>