<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

import CityList from "../components/CityList.vue";
import CityCardSkeleton from '../components/CityCardSkeleton.vue';

const router = useRouter();
const resultsApi = ref([])
const searchQuery = ref("");
const queryTimeout = ref(null);
const searchError = ref(null);

const apiKey = "2Ad6OqpWqMShACE4JRORV26fMyQl2Y1R";

const previewCity = (resultApi) => {
  const city = resultApi.LocalizedName.toLowerCase(); 
  const state = resultApi.Country.LocalizedName.toLowerCase();
  router.push({
    name: 'cityView',
    params:{ state, city},
    query:{
      key: resultApi.Key,
      preview: true
    }
  })
}
const fetchSearchResults = async () => {
  console.log(searchQuery.value)
  return await axios.get(
  `http://dataservice.accuweather.com/locations/v1/cities/autocomplete?apikey=${apiKey}&q=${searchQuery.value}`,
)};


const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async() => {
    if (searchQuery.value)
    {
      try{
        const result = await fetchSearchResults(searchQuery)
        resultsApi.value = result.data;
        return;
      } catch {
        searchError.value = true
      }      
    }
    resultsApi.value = null
  }, 300)
}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b
        focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]" 
        v-model="searchQuery"
        @input="getSearchResults"
      />
      <ul v-if="searchQuery" class="absolue bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p v-if="resultsApi.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li v-for="result in resultsApi" :key="result.Key"
          class="py-2 cursor-pointer hover:opacity-80 hover:text-red-400"
          @click="previewCity(result)"
          >
            {{ result.LocalizedName }}, {{ result.AdministrativeArea.LocalizedName }}, {{ result.Country.LocalizedName }} ({{ result.Country.ID }})
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>
