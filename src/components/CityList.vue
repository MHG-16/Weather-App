<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>
    <p v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in the field above.
    </p>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

import CityCard from "./CityCard.vue";
import { useRouter } from 'vue-router';

const savedCities = ref([]);
const apiKey = import.meta.env.VITE_API_KEY_ACCU_WEATHER;

const getCities = async() => {
        if(localStorage.getItem("savedCities")) {
            savedCities.value = JSON.parse(
                localStorage.getItem("savedCities")
            );

            const requests = [];
            savedCities.value.forEach((city) => {
                requests.push(
                    axios.get(
                        `http://dataservice.accuweather.com/currentconditions/v1/${city.key}?apikey=${apiKey}`
                    )
                )
            });

            const weatherData = await Promise.all(requests);
            // Flicker Delay
            await new Promise((res) => setTimeout(res, 1000))
            weatherData.forEach((value, index) => {
                savedCities.value[index].weather = value.data[0];
            });
        }
}

await getCities();

const router = useRouter();

const goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: {state: city.state, city: city.city},
        query:{
            key: city.key,
            id: city.id
        }
    })
}
</script>
