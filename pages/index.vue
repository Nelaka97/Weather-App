<template>
<Header />
    <div class="bg-cover bg-center bg-no-repeat" :style="{ 'background-image': 'url(' + backgroundImage + ')' }">
      <div class="pt-16 pb-16 mx-4 sm:mx-8 md:mx-12 lg:mx-16">
        <div class="flex flex-wrap">
          <!-- Card -->
          <div class="w-full lg:w-1/2 mb-4 lg:mb-0 lg:pr-8 sm:pb-12 xs:pb-12">
            <div class="card text-center shadow-xl glass">
              <figure><img src="https://cdn.suwalls.com/wallpapers/world/riga-44659-2560x1600.jpg" alt="city" />
              </figure>
              <div class="card-body">
                <h2 class="text-bold text-3xl">
                  RIGA
                  <br/><div v-if="currentTemperature !== null" class="badge badge-info badge-lg text-cyan-50">{{
                    currentTemperature }}°C
                  </div>
                </h2>
                <p>
                  <DateTime />
                </p>
                <div v-if="weatherData !== null">
                  <p>Humidity: <span class="badge badge-lg">{{ currentHumidity }}%</span> Wind Speed: <span class="badge badge-lg">{{ currentWindSpeed }} (m/s)</span></p>
                </div>
              </div>
            </div>
          </div>

          <!-- Table -->
          <div class="w-full lg:w-1/2">
            <div class="text-center mb-4">
              <span class="text-xl">Next 7 Days Weather Forecast</span>
            </div>
            <TableComponent v-if="weatherData" :weatherData="weatherData" />
            <div v-else>
              <p class="text-center pt-16">Forcast is loading...</p>
            </div>
          </div>
        </div>
        <CityWeather />
      </div>
    </div>
  <Footer />
</template>

<script>
import DateTime from '@/components/CurrentDate&Time.vue';
import TableComponent from '@/components/TableComponent.vue';
import Header from '~/components/Header.vue';
import Footer from '~/components/Footer.vue';
import CityWeather from '~/components/CityWeather.vue';

export default {
  async created() {
    try {
      const response = await fetch("https://api.open-meteo.com/v1/forecast?latitude=56.9677&longitude=24.1056&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m");
      if (!response.ok) {
        throw new Error('Failed to fetch data');
      }
      const data = await response.json();
      this.weatherData = data.hourly;
      this.currentWeatherData = data.current;

      // Set current weather
      this.setCurrentWeather();

    } catch (error) {
      console.error(error);
      this.errorMessage = 'Failed to fetch data. Please try again later.';
    }
  },
  methods: {
    setCurrentWeather() {
      // Get current date and time
      const currentDate = new Date();

      // Add 2 hours to the current date to adjust the UTC to local time in next step
      currentDate.setHours(currentDate.getHours() + 2);

      // Get the formatted date and time string 
      const formattedDateTime = currentDate.toISOString().slice(0, 13) + ':00';
      console.log("Formatted Date Time with 3 hours added:", formattedDateTime);

      // Set current date and time
      this.currentDateTime = formattedDateTime;

      // Find current weather based on the current date and time
      if (this.weatherData && Array.isArray(this.weatherData.time)) {
        const index = this.weatherData.time.findIndex(entry => entry === formattedDateTime);
        if (index !== -1) {
          this.currentTemperature = ((this.currentWeatherData.temperature_2m + this.weatherData.temperature_2m[index]) / 2).toFixed(1);
          this.currentWindSpeed = ((this.currentWeatherData.wind_speed_10m + this.weatherData.wind_speed_10m[index]) / 2).toFixed(1);
          this.currentHumidity = this.weatherData.relative_humidity_2m[index];
        } else {
          this.currentTemperature = null; // Temperature data not found for the current date and time
          this.currentHumidity = null;
          this.currentWindSpeed = null;
        }
      } else {
        this.currentTemperature = null; // Temperature data not available
        this.currentHumidity = null;
        this.currentWindSpeed = null;
      }
    },
    selectRandomBackgroundImage() {
      // Generate a random index to select a random image from the array
      const randomIndex = Math.floor(Math.random() * this.images.length);
      // Construct the URL of the randomly selected background image
      this.backgroundImage = `/${this.images[randomIndex]}`;
    }
  },
  mounted() {
    // Select a random background image when the component is mounted
    this.selectRandomBackgroundImage();
  },
  components: {
    // Register components
    DateTime,
    TableComponent,
    Header,
    Footer,
    CityWeather
  },
  data() {
    return {
      weatherData: null,
      currentWeatherData: null,
      currentTemperature: null,
      currentDateTime: null,
      currentWindSpeed: null,
      errorMessage: null,
      images: ['bg-1.svg', 'bg-2.svg', 'bg-3.jpg', 'bg-4.svg'],
      backgroundImage: '' // Will hold the URL of the randomly selected background image
    };
  },
}
</script>
