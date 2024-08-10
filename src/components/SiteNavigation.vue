<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg z-40">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">Weather</p>
                </div>
            </RouterLink>
            
            <div class="flex gap-3 flex-1 justify-end">
                <i 
                    class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" 
                    @click="toggleModal"
                ></i>
                <i 
                    class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="addCity"
                    v-if="route.query.preview"
                ></i>
            </div>

            <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
                <div class="text-black px-4">
                    <h1 class="text-2xl mb-1 flex justify-center">ABOUT</h1>
                    <p class="mb-4">
                        This application called "Weather" allows you to track the current and
                        future weather of cities of your choosing.
                    </p>
                    <h2 class="text-2xl flex justify-center">HOW IT WORKS?</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>
                            Search for your city by typing its name into 
                            the search field.
                        </li>
                        <li>
                            Choose a city from the list of results to view 
                            the current weather in that location.
                        </li>
                        <li>
                            Click the "+" symbol in the upper right corner 
                            to follow the city. This will save the city to 
                            the home page for later viewing.
                        </li>
                    </ol>

                    <h2 class="text-2xl flex justify-center">REMOVING A CITY</h2>
                    <p>
                        You may easily remove a city from your tracking 
                        list by selecting it directly from the homepage. 
                        There will be an option to remove the city at the 
                        bottom of the page.
                    </p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from './BaseModal.vue';
import { ref } from 'vue';
import { uid } from 'uid';

const route = useRoute();
const router = useRouter();

const modalActive = ref(null);
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};

const savedCities = ref([]);

const addCity = () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(
            localStorage.getItem('savedCities')
        )
    }

    const locationObj = {
        id: uid(),
        state: route.params.state.replace(/_/g, " "),
        city: route.params.city.replace(/_/g, " "),
        coordinates: {
            lat: route.query.lat,
            lng: route.query.lng,
        },
    };

    // Check if the city already exists in the savedCities array
    const existingCity = savedCities.value.find(city => 
        city.state === locationObj.state && 
        city.city === locationObj.city
    );

    if (existingCity) {
        // Redirect to the existing city if found
        router.push({
            name: "cityView",
            params: {
                state: existingCity.state,
                city: existingCity.city,
            },
            query: {
                id: existingCity.id,
                lat: existingCity.coordinates.lat,
                lng: existingCity.coordinates.lng,
            },
        });
    } else {
        // If city doesn't exist, add it to the savedCities
        savedCities.value.push(locationObj);
        localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

        let query = Object.assign({}, route.query);
        delete query.preview;
        query.id = locationObj.id;
        router.replace({ query });
    }
};
</script>