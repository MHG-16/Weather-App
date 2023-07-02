<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-3xl capitalize">
                {{ city.city }}
            </h2>
            <h3 class="capitalize">{{ city.state }}</h3>
        </div>
        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.Temperature.Metric.Value) }}&deg;
            </p>
            <img class="w-[50px] h-auto" 
                :src="`https://developer.accuweather.com/sites/default/files/${city.weather.WeatherIcon < 10 ? '0' : ''}${city.weather.WeatherIcon}-s.png`"
            />
        </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        required: true
    }
})
</script>
