<template>
  <div>
    <form @submit.prevent="searchWeather">
      <label for="city">Select City:</label>
      <select v-model="selectedCity" id="city">
        <option v-for="city in cities" :key="city.name" :value="city">
          {{ city.name }}
        </option>
      </select>
      <button type="submit">Show Current Weather</button>
    </form>
    
    <div v-if="cityWeather">
      <h2>{{ cityWeather.name }}</h2>
      <p>Current Temperature: {{ cityWeather.temperature_2m }}°C</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedCity: null,
      cities: [
        { name: 'London', latitude: 51.5074, longitude: -0.1278 },
        { name: 'Paris', latitude: 48.8566, longitude: 2.3522 },
        { name: 'Berlin', latitude: 52.5200, longitude: 13.4050 },
      ],
      cityWeather: null
    };
  },
  methods: {
    async searchWeather() {
      try {
        const response = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${this.selectedCity.latitude}&longitude=${this.selectedCity.longitude}&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`);
        if (!response.ok) {
          throw new Error('Failed to fetch data');
        }
        const data = await response.json();
        this.cityWeather = data.current;
      } catch (error) {
        console.error(error);
        // Handle error
      }
    }
  }
};
</script>
