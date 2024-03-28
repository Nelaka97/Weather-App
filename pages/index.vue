<template>
  <div>
    <Header />
    <div class="bg-cover bg-center bg-no-repeat" :style="{ 'background-image': 'url(\'/bg.svg\')' }">
      <div class="pt-16 pb-16 mx-4 sm:mx-8 md:mx-12 lg:mx-16 justify-center">
        <div class="border-8 border-solid border-warning p-2 lg:w-3/4 mx-auto mb-20">
          <CityWeather />
        </div>
        <div class="flex flex-wrap lg:w-3/4 mx-auto">
          <!-- Card -->
          <div class="w-full lg:w-2/4 mb-4 lg:mb-0 lg:pr-8 sm:pb-12 xs:pb-12 justify-center">
            <div class="card text-center shadow-xl glass">
              <figure>
                <img src="https://cdn.suwalls.com/wallpapers/world/riga-44659-2560x1600.jpg" alt="city" />
              </figure>
              <div class="card-body">
                <p class="text-bold">Your Current Location</p>
                <div v-if="userExactPlace && userCity" class="text-bold text-3xl">
                  <span v-if="userExactPlace !== userCity">{{ userExactPlace }}</span>
                  <span v-if="userExactPlace !== userCity && userCity">, </span>
                  <span>{{ userCity }}</span>
                </div>

                <div v-else>Loading...</div>

                <h2>
                  <div v-if="userExactPlace && userCity" class="badge badge-info badge-lg text-cyan-50 text-bold">
                    {{ currentWeatherData.temperature_2m }}°C
                  </div>
                </h2>
                <span v-if="userExactPlace && userCity">
                  <DateTime />
                </span>
                <div v-if="userExactPlace && userCity">
                  <p>Wind Speed: <span class="badge badge-lg">{{ currentWeatherData.wind_speed_10m }} (m/s)</span></p>
                </div>
              </div>
            </div>
          </div>

          <!-- Table -->
          <div class="w-full lg:w-2/4 justify-center">
            <div class="text-center mb-8 mt-8">
              <span class="text-2xl text-bold">{{ userExactPlace }} - Next 7 Days Weather Forecast</span>
            </div>
            <TableComponent v-if="weatherData" :weatherData="weatherData" />
            <div v-else>
              <p class="text-center pt-16">Forecast is loading...</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer />
  </div>
</template>

<script>
// Import necessary components
import DateTime from '@/components/CurrentDate&Time.vue';
import TableComponent from '@/components/TableComponent.vue';
import Header from '~/components/Header.vue';
import Footer from '~/components/Footer.vue';
import CityWeather from '~/components/CityWeather.vue';

export default {
  async created() {
    try {
      // Get user's current location
      navigator.geolocation.getCurrentPosition(
        async (position) => {
          const { latitude, longitude } = position.coords;

          // Log latitude and longitude
          console.log('Latitude:', latitude);
          console.log('Longitude:', longitude);

          // Make API request with user's location to fetch weather data
          const weatherResponse = await fetch(
            `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`
          );

          if (!weatherResponse.ok) {
            throw new Error('Failed to fetch weather data');
          }

          const weatherData = await weatherResponse.json();
          this.weatherData = weatherData.hourly;
          this.currentWeatherData = weatherData.current;

          // Make API request with user's location to fetch city name
          const locationResponse = await fetch(
            `https://api.bigdatacloud.net/data/reverse-geocode-client?latitude=${latitude}&longitude=${longitude}&localityLanguage=en`
          );

          if (!locationResponse.ok) {
            throw new Error('Failed to fetch location data');
          }

          const locationData = await locationResponse.json();
          this.userExactPlace = locationData.locality;
          this.userCity = locationData.city;

        },
        (error) => {
          console.error('Error getting user location:', error);
          this.errorMessage = 'Failed to get user location.';
        }
      );
    } catch (error) {
      console.error(error);
      this.errorMessage = 'Failed to fetch data. Please try again later.';
    }
  },
  components: {
    // Register components
    DateTime,
    TableComponent,
    Header,
    Footer,
    CityWeather,
  },
  data() {
    return {
      weatherData: null,
      currentWeatherData: null,
      userExactPlace: null,
      userCity: null,
      errorMessage: null
    };
  },
};
</script>
