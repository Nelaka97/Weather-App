<template>
  <div class="flex justify-center pt-16">
    <form @submit.prevent="searchWeather" class="dropdown">
      <select v-model="selectedCity" id="city" class="btn m-1">
        <option :value="null" disabled>Select City</option>
        <option v-for="city in cities" :key="city.name" :value="city">
          {{ city.name }}
        </option>
      </select>
      <button class="btn btn-warning" type="submit">Show Current Weather</button>
    </form>
  </div>

  <div class="stats shadow flex flex-col md:flex-row mt-8 lg:w-3/4 mx-auto" v-if="cityWeather">

    <div class="stat mb-4 md:mb-0 md:mr-4">
      <div class="stat-figure text-secondary">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
          class="inline-block w-8 h-8 stroke-current">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
        </svg>
      </div>
      <div class="stat-title text-neutral">City</div>
      <div class="stat-value">{{ cityName }}</div>
    </div>

    <div class="stat mb-4 md:mb-0 md:mr-4">
      <div class="stat-figure text-secondary">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
          class="inline-block w-8 h-8 stroke-current">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
        </svg>
      </div>
      <div class="stat-title text-neutral">Temperature</div>
      <div class="stat-value">{{ cityWeather.temperature_2m }}°C</div>
    </div>

    <div class="stat mb-4 md:mb-0 md:mr-4">
      <div class="stat-figure text-secondary">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
          class="inline-block w-8 h-8 stroke-current">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4">
          </path>
        </svg>
      </div>
      <div class="stat-title text-neutral">Wind Speed</div>
      <div class="stat-value">{{ cityWeather.wind_speed_10m }} km/h</div>
    </div>
  </div>
</template>



<script>
export default {
  data() {
    return {
      selectedCity: null,
      cityName: 'Select City',
      cities: [
        { name: 'London', latitude: 51.5074, longitude: -0.1278 },
        { name: 'Paris', latitude: 48.8566, longitude: 2.3522 },
        { name: 'Berlin', latitude: 52.5200, longitude: 13.4050 },
        { name: 'Rome', latitude: 41.9028, longitude: 12.4964 },
        { name: 'Madrid', latitude: 40.4168, longitude: -3.7038 },
        { name: 'Amsterdam', latitude: 52.3676, longitude: 4.9041 },
        { name: 'Brussels', latitude: 50.8503, longitude: 4.3517 },
        { name: 'Vienna', latitude: 48.2082, longitude: 16.3738 },
        { name: 'Lisbon', latitude: 38.7223, longitude: -9.1393 },
        { name: 'Athens', latitude: 37.9838, longitude: 23.7275 }
      ],
      cityWeather: null
    };
  },
  methods: {
    hydrateUrl(lat, lon) {
      return `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`
    },
    async searchWeather() {
      try {
        const response = await fetch(this.hydrateUrl(this.selectedCity.latitude, this.selectedCity.longitude));
        if (!response.ok) {
          throw new Error('Failed to fetch data');
        }
        const data = await response.json();
        this.cityWeather = data.current;
        this.cityName = this.selectedCity.name;
      } catch (error) {
        console.error(error);
        // Handle error
      }
    }
  }
};
</script>
