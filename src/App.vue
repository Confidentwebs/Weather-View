<template>
  <div class="app">
    <!-- Background weather objects -->
    <div class="weather-background">
      <div class="sun"></div>
      <!-- Clouds floating around sun -->
      <div class="cloud" v-for="n in 5" :key="'cloud'+n" :style="{'--i': n}"></div>
      <!-- Raindrops -->
      <div class="raindrop" v-for="n in 20" :key="'rain'+n" :style="{'--i': n}"></div>
    </div>

    <div
      class="weather-container"
      :class="{ searched: weatherStore.currentWeather }"
    >
      <!-- Card before search -->
      <div v-if="!weatherStore.currentWeather" class="weather-card">
        <h1>Know Your Weather</h1>

        <div class="search">
          <input
            type="text"
            placeholder="Enter city name"
            v-model="cityInput"
            @keyup.enter="getWeather"
          />
          <button @click="getWeather">Search</button>
        </div>
      </div>

      <!-- Full UI after search -->
      <div v-else class="weather-full">
        <div class="search-bar">
          <input
            type="text"
            placeholder="Search another city"
            v-model="cityInput"
            @keyup.enter="getWeather"
          />
          <button @click="getWeather">Search</button>
        </div>

        <div class="current-weather">
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
            <li v-for="(day, index) in weatherStore.forecast" :key="index">
              <span>{{ day.dt_txt }}</span>
              <span>{{ day.main.temp }} °C</span>
              <span>{{ day.weather[0].description }}</span>
            </li>
          </ul>
        </div>
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
/* App background */
.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
  font-family: "Segoe UI", Tahoma, sans-serif;
  overflow: hidden;
  position: relative;
  color: #f8fafc;
}

/* Background weather objects container */
.weather-background {
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
  top: 0;
  left: 0;
  z-index: 1;
}

/* Sun */
.sun {
  position: absolute;
  top: 15%;
  left: 50%;
  width: 120px;
  height: 120px;
  margin-left: -60px;
  background: radial-gradient(circle, #ffe066 0%, #ffba08 70%);
  border-radius: 50%;
  filter: blur(12px);
  z-index: 1;
  box-shadow: 0 0 80px rgba(255, 190, 0, 0.6);
}

/* Clouds */
.cloud {
  position: absolute;
  background: rgba(255, 255, 255, 0.25);
  border-radius: 50%;
  width: 100px;
  height: 60px;
  filter: blur(6px);
  top: 18%; /* roughly same line as sun */
  left: calc(-200px - var(--i) * 100px); /* spread clouds horizontally */
  animation: float 10s linear infinite;
  z-index: 2;
}

.cloud::before,
.cloud::after {
  content: '';
  position: absolute;
  background: rgba(255, 255, 255, 0.25);
  width: 60px;
  height: 60px;
  border-radius: 50%;
  top: -15px;
}

.cloud::after {
  width: 80px;
  height: 80px;
  top: 10px;
  left: 30px;
}

/* Floating animation */
@keyframes float {
  0% { transform: translateX(-200px); }
  100% { transform: translateX(120vw); }
}

/* Raindrops */
.raindrop {
  position: absolute;
  width: 2px;
  height: 12px;
  background: rgba(255, 255, 255, 0.6);
  top: -10px;
  left: calc(5% * var(--i) + 20px);
  animation: rain 1s linear infinite;
  z-index: 1;
}

@keyframes rain {
  0% { transform: translateY(-10px); opacity: 0.6; }
  100% { transform: translateY(100vh); opacity: 0; }
}

/* Container adjusts for card vs full UI */
.weather-container {
  z-index: 3;
  width: 420px;
  transition: all 0.5s ease-in-out;
}

.weather-container.searched {
  width: 90%;
  max-width: 900px;
}

/* Card before search */
.weather-card {
  padding: 2.5rem;
  border-radius: 20px;
  text-align: center;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(18px);
  -webkit-backdrop-filter: blur(18px);
  border: 1px solid rgba(255, 255, 255, 0.25);
  box-shadow: 0 25px 60px rgba(0, 0, 0, 0.35);
}

/* Full weather UI after search */
.weather-full {
  padding: 2rem;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(15px);
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  text-align: center;
}

.search, .search-bar {
  display: flex;
  gap: 0.6rem;
  justify-content: center;
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
  margin-top: 1rem;
}

.temp {
  font-size: 3rem;
  font-weight: bold;
}

.forecast {
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

@media (max-width: 768px) {
  .weather-container.searched {
    width: 95%;
  }

  .weather-full {
    padding: 1rem;
  }
}
</style>
