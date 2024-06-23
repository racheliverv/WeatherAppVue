<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
const searchQuery = ref('');
const searchError = ref(false);
const openCageAPIKey = "5bfd534c4ad142fdace7a503589038ad";
const router = useRouter();
const queryTimeout = ref(null);
const openCageSearchResults = ref(null);

const previewCity = (searchResult) => {
  const [mainland, country, city] = [searchResult.components.continent, searchResult.components.country, searchResult.components.city]
  console.log(mainland, city, country);
  router.push({
  name: "cityView",
  params: { mainland, country, city },
  query: {
    lat: searchResult.geometry.lat,
    lng: searchResult.geometry.lng,
    preview: true,
  },
})
}

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value && searchQuery.value.length > 2) {
      try {
        const result = await axios.get(
          `https://api.opencagedata.com/geocode/v1/json?q=${searchQuery.value}&key=${openCageAPIKey}`
        );
        openCageSearchResults.value = result.data.results;
        searchError.value = false; // Reset error on successful search
      } catch {
        searchError.value = true; // Set error if the request fails
      }
    } else {
      openCageSearchResults.value = null; // Clear results if search query is empty
    }
  }, 300);
};
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" v-model="searchQuery" @input="getSearchResults" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0#004E71]" />
    </div>
    <div v-if="searchError" class="text-red-500">Error fetching search results</div>
    <ul v-if="openCageSearchResults"
      class="bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px] cursor-pointer">
      <li @click="previewCity(result)" v-for="result in openCageSearchResults"
        :key="result.annotations.DMS.lat + result.annotations.DMS.lng">
        {{ result.formatted }}
      </li>
    </ul>
  </main>
</template>
