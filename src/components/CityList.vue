<template>
    <div>
        <div v-for="city in savedCities" :key="city.id">
            <CityCard :city="city" @click="goToCityView(city)" />
        </div>

        <p v-if="savedCities.length === 0">
            No locations added. To start tracking a location, search in
            the field above.
        </p>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router'

const savedCities = ref([])
const getSavedCities = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

        const requests = []
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(
                    `https://api.openweathermap.org/data/2.5/weather?units=metric&lat=${city.cords.lat}&lon=${city.cords.lon}&appid=1fbbdd6c3a2247f9a40286269cf58241`
                )
            )
        })

        const weatherData = await Promise.all(requests)

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data
        })
    }
}

await getSavedCities()
const router = useRouter()
const goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: {
            city: city.city,
            state: city.state,
        },
        query: {
            lat: city.cords.lat,
            lon: city.cords.lon,
        },
    })
}
</script>