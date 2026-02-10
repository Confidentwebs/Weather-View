<template>
  <div class="app">
    <div class="weather-card">
      <h1>Weather Dashboard</h1>

      <div class="search">
        <input
          type="text"
          placeholder="Enter city name"
          v-model="cityInput"
          @keyup.enter="getWeather"
        />
        <button @click="getWeather">Search</button>
      </div>

      <p v-if="weatherStore.currentWeather" class="location">
        Showing weather for {{ weatherStore.currentWeather.name }}
      </p>

      <div v-if="weatherStore.currentWeather" class="current-weather">
        <h2>{{ weatherStore.currentWeather.name }}</h2>
        <p class="description">
          {{ weatherStore.currentWeather.weather[0].description }}
        </p>
        <p class="temp">
          {{ weatherStore.currentWeather.main.temp }} °C
        </p>
      </div>

      <div v-if="weatherStore.forecast.length" class="forecast">
        <h3>5-Day Forecast</h3>
        <ul>
          <li
            v-for="(day, index) in weatherStore.forecast"
            :key="index"
          >
            <span>{{ day.dt_txt }}</span>
            <span>{{ day.main.temp }} °C</span>
            <span>{{ day.weather[0].description }}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useWeatherStore } from '@/stores/weather'

const cityInput = ref('')
const weatherStore = useWeatherStore()

function getWeather() {
  if (cityInput.value.trim()) {
    weatherStore.fetchWeather(cityInput.value)
    cityInput.value = ''
  }
}
</script>

<style scoped>
.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
  font-family: "Segoe UI", Tahoma, sans-serif;
}

.weather-card {
  width: 420px;
  padding: 2.5rem;
  border-radius: 20px;
  text-align: center;

  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(18px);
  -webkit-backdrop-filter: blur(18px);

  border: 1px solid rgba(255, 255, 255, 0.25);
  box-shadow: 0 25px 60px rgba(0, 0, 0, 0.35);
  color: #f8fafc;

  /* 👇 IMPORTANT FIX */
  max-height: calc(100vh - 6rem);
  overflow: hidden;
}


.search {
  display: flex;
  gap: 0.6rem;
  margin-bottom: 1.2rem;
}

input {
  flex: 1;
  padding: 0.65rem 0.8rem;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

button {
  padding: 0.65rem 1.2rem;
  border-radius: 10px;
  border: none;
  background: rgba(79, 70, 229, 0.85);
  color: white;
  cursor: pointer;
}

.current-weather {
  margin-top: 1.5rem;
}

.temp {
  font-size: 3rem;
  font-weight: bold;
}

.forecast {
  margin-top: 2rem;

  max-height: 260px;
  overflow-y: auto;
  padding-right: 4px;
}
.forecast::-webkit-scrollbar {
  width: 6px;
}

.forecast::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.25);
  border-radius: 10px;
}


ul {
  list-style: none;
  padding: 0;
}

li {
  display: grid;
  grid-template-columns: 1.4fr 0.6fr 1fr;
  gap: 0.4rem;
  padding: 0.7rem;
  background: rgba(255, 255, 255, 0.12);
  border-radius: 12px;
  margin-bottom: 0.5rem;
}

@media (max-height: 750px) {
  .weather-card {
    max-height: calc(100vh - 4rem);
    padding: 2rem 1.5rem;
  }

  .forecast {
    max-height: 200px;
  }
}

</style>
