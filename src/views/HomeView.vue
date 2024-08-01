<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input 
        type="text"
        v-model="searchQuery"
        @input="getSearchResults" 
        placeholder="Search for a city or state" 
        class="py-2 px-1 w-full bg-transparent border-b focus:bg-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md px-1 py-2 top-[66px] rounded-md" v-if="mapboxSearchResults">
        <p v-if="searchError">Sorry, something when wrong. Please try again.</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">There were no results for your query, try a new keyword.</p>
        <template v-else>
          <li 
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.properties.full_address }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <p>Loading...</p>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import CityList from '@/components/CityList.vue';
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxAccessToken = import.meta.env.VITE_MAPBOX_ACCESS_TOKEN;
const mapboxSearchResults = ref(null);
const searchError = ref(false);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value); // lazy search
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value != "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}&types=place&access_token=${mapboxAccessToken}`);
        // Filter the results to include only those with at least 3 comma-separated parts
        mapboxSearchResults.value = result.data.features.filter(feature => 
          feature.properties.full_address.split(',').length >= 3
        );
        searchError.value = false;
      } catch {
        searchError.value = true;
        mapboxSearchResults.value = null;
      }

      return;
    }
  }, 300);
}

const router = useRouter();
const previewCity = (searchResult) => {
  console.log(searchResult);
  const [city, state] = searchResult.properties.full_address.split(',');
  router.push({
    name: "cityView",
    params: { 
      state: state.trim().replace(" ", "_"), city: city.replace(" ", "_")},
    query: {
      lat: searchResult.properties.coordinates.latitude,
      lng: searchResult.properties.coordinates.longitude,
      preview: true,
    }
  });
}
</script>
