

<template>
  <main class="container text-white">
   <div class="pt-4 mb-8 relative">
    <input
    v-model="searchQuery"
    @input="getSearchResults"
    type="text" placeholder="Search for a city of state" class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
    <ul v-if="mapboxSearchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
      <p v-if="searchError">Sorry, something went wrong, please try again.</p>
      <p v-if="!serverError && mapboxSearchResults.length === 0">No results match your query, try a different term.</p>

      <template v-else>
        <li
        @click="previewCity(searchResult)"
         v-for="searchResult in mapboxSearchResults" 
         :key="searchResult.id" class="py-2 cursor-pointer">{{ searchResult.place_name }}</li>
      </template>
    </ul>
   </div>
   <div class="flex flex-col gap-4">
    <Suspense>
      <Citylist></Citylist>
      <template #fallback>
        <CityCardSkeleton />
      </template>
    </Suspense>
   </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import Citylist from '../components/Citylist.vue';
import CityCardSkeleton from '../components/CityCardSkeleton.vue';

const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxAPIkey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q"
const mapboxSearchResults = ref(null);
const searchError = ref(null)
const router = useRouter()

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async() => {
    if(searchQuery.value !== '') {
      try {
        const results = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIkey}&types=place`)
        mapboxSearchResults.value = results.data.features;
      } catch (error) {
        searchError.value = true
      }
      
      return;
    }
    mapboxSearchResults.value = nulll;
  },300)
}

const previewCity = (searchResult) => {
  // console.log(searchResult)
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: 'cityView',
    params: {state: state.replaceAll(" ",""), city: city},
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}
</script>