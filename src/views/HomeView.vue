<template>
  <main class="container text-white lg:w-6/12 lg:my-8 ">
      <div class="pt-4 md-8 relative flex flex-col items-center justify-center">
          <input 
            v-model="searchQuery"
            @input="getSearchResults"
            type="text"
            placeholder="seach for a city or state"
            class="py-2 px-1 lg:w-7/12 w-full bg-transparent border-b text-center focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">

            <ul 
              v-if="searchResults"
              class="absolute bg-weather-secondary text-white lg:w-6/12 text-center shadow-md py-2 px-1 top-[66px]">

              <p v-if="searchError">Sorry, something went wrong, please try again.</p>

              <p v-if="!searchError && searchResults.length === 0">
                No esults match your query, try a different term.
              </p>

             <template v-else>
                 <li 
                    v-for="searchResult in searchResults" 
                    :key="searchResult.place_id" 
                    @click="selectSearchResult(searchResult)"
                    class="py-2 cursor-pointer hover:bg-weather-primary transition">
                    {{ searchResult.display_name }}
                </li>
             </template>

            </ul>

            <div v-if="weatherData" class="my-6 p-4 bg-weather-secondary rounded-lg shadow-lg max-w-md w-full">
              <h2 class="text-2xl font-semibold mb-4 border-b pb-2 border-weather-primary">Current Weather</h2>
              <div class="flex flex-col space-y-2">
                <div class="flex justify-between">
                  <span class="font-medium">🌡️ Temperature:</span>
                  <span>{{ weatherData.temperature }}°C</span>
                </div>
                <div class="flex justify-between">
                  <span class="font-medium">💨 Windspeed:</span>
                  <span>{{ weatherData.windspeed }} km/h</span>
                </div>
                <div class="flex justify-between">
                  <span class="font-medium">🕒 Time:</span>
                  <span>{{ new Date(weatherData.time).toLocaleTimeString() }}</span>
                </div>
              </div>
            </div>


      </div>
  </main>
</template>


<script setup>
import { ref } from 'vue'
import axios from 'axios'

const searchQuery = ref('')
const queryTimeout = ref(null)
const searchResults = ref(null)

const weatherData = ref(null)

const searchError = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://nominatim.openstreetmap.org/search?q=${searchQuery.value}&format=json&limit=5`, {
          headers: {
            'Accept-Language' : 'en',
            'User-Agent' : 'Weather-app/1.0(prajapatikkaushik6392@gmail.com)'
          }
        })
        searchResults.value = result.data
      } catch{
        searchError.value = true
      }
    } else {
      searchResults.value = null
    }
  }, 300)
}
const selectSearchResult = (result) => {
  searchQuery.value = result.display_name 
  searchResults.value = null 

  getWeatherData(result.lat ,result.lon)
}

const getWeatherData = async (lat, lon) => {
  const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`
  try {
    const response = await axios.get(url)
    weatherData.value = response.data.current_weather
  } catch (error) {
    console.error("Failed to fetch weather data:", error)
  }
}


</script>



<!-- <script setup>

 import{ref} from "vue"
 import axios from "axios";

 const mapboxAPIKey =""
 
 const searchQuery = ref("")
 const queryTimeout = ref(null)
 const mapboxSearchResults = ref(null)


  const getSearchResults = () => {
    clearTimeout(queryTimeout.value)
    queryTimeout.value = setTimeout(async () => {
      if(searchQuery.value !== ""){
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place `);
        mapboxSearchResults.value = result.data.features
      }
      mapboxSearchResults.value = null
    },300)
  }

</script> -->


