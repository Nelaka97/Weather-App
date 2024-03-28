<template>
  <div class="pt-8 pb-8">
    <div class="flex justify-center">
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

    <div class="flex flex-wrap justify-center pt-16" v-if="cityWeather">
      <!-- Table -->
      <div class="w-full lg:w-3/4">
        <div class="text-center mb-8">
          <span class="text-2xl text-bold">{{ cityName }} - Next 7 Days Weather Forecast</span>
        </div>

        <div class="overflow-x-auto">
          <table class="table table-xs table-pin-rows table-pin-cols text-center glass">
            <thead class="text-slate-700">
              <tr>
                <th>Date</th>
                <th>Temperature (°C)</th>
                <th>Relative Humidity (%)</th>
                <th>Wind Speed (m/s)</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(hourData, index) in paginatedData" :key="index">
                <td>{{ hourData.date }}</td>
                <td>
                  <div v-if="hourData.temperature <= 4" class="badge badge-info badge-outline badge-sm">
                    {{ hourData.temperature.toFixed(1) }} °C
                  </div>
                  <div v-else-if="hourData.temperature > 4 && hourData.temperature <= 8"
                    class="badge badge-success badge-outline badge-sm">
                    {{ hourData.temperature.toFixed(1) }} °C
                  </div>
                  <div v-else-if="hourData.temperature > 8 && hourData.temperature <= 15"
                    class="badge badge-outline badge-sm text-orange-600">
                    {{ hourData.temperature.toFixed(1) }} °C
                  </div>
                  <div v-else class="badge badge-error badge-outline badge-sm">
                    {{ hourData.temperature.toFixed(1) }} °C
                  </div>
                </td>
                <td>{{ hourData.relativeHumidity }}</td>
                <td>{{ hourData.windSpeed }}</td>
              </tr>
            </tbody>
          </table>
          <div class="flex justify-center pt-8">
            <button @click="prevPage" :disabled="currentPage === 1" class="btn btn-xs">Previous Day</button>
            <span class="mx-4 text-bold">{{ currentPage }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages" class="btn btn-xs">Next Day</button>
          </div>
        </div>

      </div>
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
        { name: 'Athens', latitude: 37.9838, longitude: 23.7275 },
        { name: "Colombo", latitude: 6.9271, longitude: 79.8612 },
        { name: "New York", latitude: 40.7128, longitude: -74.0060 },
        { name: "Riga", latitude: 56.9496, longitude: 24.1052 },
        { name: "Stockholm", latitude: 59.3293, longitude: 18.0686 },
        { name: "Helsinki", latitude: 60.1695, longitude: 24.9354 },
        { name: "Milan", latitude: 45.4642, longitude: 9.1900 }
      ],
      cityWeather: null,
      forecastData: null, // Add forecastData property
      currentPage: 1,
      itemsPerPage: 24,
      error: null // Add error property to store error messages
    };
  },
  methods: {
    hydrateUrl(lat, lon) {
      return `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`;
    },
    async searchWeather() {
      try {
        const response = await fetch(this.hydrateUrl(this.selectedCity.latitude, this.selectedCity.longitude));
        if (!response.ok) {
          throw new Error(`Failed to fetch data: ${response.status} ${response.statusText}`);
        }
        const data = await response.json();
        this.cityWeather = data.current;
        this.forecastData = data.hourly; // Assign fetched data to forecastData
        this.cityName = this.selectedCity.name;
        this.error = null; // Reset error if successful
      } catch (error) {
        console.error(error);
        this.error = error.message; // Set error message
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    }
  },
  computed: {
    paginatedData() {
      if (!this.forecastData) return []; // Handle scenario when forecastData is null
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.forecastData.time.slice(startIndex, endIndex).map((hourData, index) => ({
        date: hourData,
        temperature: this.forecastData.temperature_2m[startIndex + index],
        relativeHumidity: this.forecastData.relative_humidity_2m[startIndex + index],
        windSpeed: this.forecastData.wind_speed_10m[startIndex + index]
      }));
    },
    totalPages() {
      if (!this.forecastData) return 0; // Handle scenario when forecastData is null
      return Math.ceil(this.forecastData.time.length / this.itemsPerPage);
    }
  }
};
</script>
