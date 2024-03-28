<template>
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
        <div class="flex justify-center mt-4">
            <button @click="prevPage" :disabled="currentPage === 1" class="btn btn-xs">Previous Day</button>
            <span class="mx-4 text-bold">{{ currentPage }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages" class="btn btn-xs">Next Day</button>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        weatherData: {
            type: Object,
            required: true
        }
    },
    data() {
        return {
            currentPage: 1,
            itemsPerPage: 24
        };
    },
    computed: {
        paginatedData() {
            const startIndex = (this.currentPage - 1) * this.itemsPerPage;
            const endIndex = startIndex + this.itemsPerPage;
            return this.weatherData.time.slice(startIndex, endIndex).map((hourData, index) => ({
                date: hourData,
                temperature: this.weatherData.temperature_2m[startIndex + index],
                relativeHumidity: this.weatherData.relative_humidity_2m[startIndex + index],
                windSpeed: this.weatherData.wind_speed_10m[startIndex + index]
            }));
        },
        totalPages() {
            return Math.ceil(this.weatherData.time.length / this.itemsPerPage);
        }
    },
    methods: {
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
    }
};
</script>
