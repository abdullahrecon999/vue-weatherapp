<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input @input="getSearchResults" v-model="searchQuery" type="text" placeholder="Search for a city" class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]" >
      <ul v-if="searchResults?.length > 0" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <li
          @click="previewCity(searchRes)"
          v-for="searchRes in searchResults" :key="searchResults.id" class="py-2 cursor-pointer">
          {{ searchRes.display_name }}
        </li>
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

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import CityList from '../components/CityList.vue'

const router = useRouter()
const previewCity = (data) => {
  console.log(data)
  const [city, state] = data.display_name.split(',')
  router.push({ name: 'cityView', params: { city:city, state:state.replaceAll(" ","") }, query: { lat: data.lat, lon: data.lon, preview: true } })
}

const searchQuery = ref('')
const queryTimeout = ref(null)
const searchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if(searchQuery.value.length !== "") {
      const result = await axios.get(`https://geocode.maps.co/search?q=${searchQuery.value}&limit=4`, {
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
      })
      searchResults.value = result.data
      return
    }
    searchResults.value = null
  }, 300)
}
</script>

