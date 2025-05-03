<template>
  <div class="weather-container">
    <h1>⛅ Weather App</h1>

    <form @submit.prevent="fetchWeather">
      <input
        type="text"
        v-model="city"
        placeholder="Enter city name"
      />
      <button type="submit">Get Weather</button>
    </form>

    <div v-if="loading" class="status">Loading...</div>
    <div v-else-if="error" class="status error">{{ error }}</div>

    <div v-else-if="weather" class="weather-result">
      <h2>{{ weather.name }}, {{ weather.sys.country }}</h2>
      <p class="temp">{{ Math.round(weather.main.temp) }}°C</p>
      <p class="desc">{{ weather.weather[0].description }}</p>
      <img
        :src="`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`"
        :alt="weather.weather[0].description"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
const API_KEY = import.meta.env.VITE_WEATHER_API_KEY;

const city = ref("");
const weather = ref(null);
const error = ref('');
const loading = ref(false);

const fetchWeather = async () => {
  if (!city.value.trim()) {
    error.value = 'Please enter a city name.';
    return;
  }

  loading.value = true;
  error.value = '';
  weather.value = null;

  try {
    let url = `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&appid=${API_KEY}&units=metric`
    const res = await fetch(url);

    if (!res.ok) {
      throw new Error("City not found");
    }

    const data = await res.json();
    weather.value = data;
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
}

</script>