<template>
  <div class="home">
    <h1>Weather App</h1>
    <div v-if="error">{{ error }}</div>
    <ul v-if="weather">
      <li>{{ "Location: " + weather.timezone }}</li>
      <li>
        {{ weather.current.weather[0].main }}
        <img :src="iconUrl" />
      </li>
      <li>{{ iconUrl }}</li>
      <li>Temp: {{ currentTemp }}</li>
      <li>Hum: {{ currentHum  }}</li>
    </ul>
    <!-- Day Components -->
    <div v-if="daily !== null && daily.length" :key="day.dt">
      <Day :day="day" />
    </div>
    <!-- 
    <div v-for="(day, index) in daily" :key="index">
    </div>
    -->
    <ul>
      <li>{{ counter }}</li>
      <li>{{ counterTimesTwo }}</li>
      <li><button @click="addCounter">Add Counter</button></li>
    </ul>
  </div>
</template>

<script>
import Day from '@/components/Day'
import axios from "axios";

export default {
  name: "Home",
  components: {
    Day,
  },
  data() {
    return {
      weather: null,
      error: null,
      counter: 0,
    };
  },

  created() {
    const apiKey = process.env.VUE_APP_WEATHER_API_KEY;
    let lat = 48.210033;
    let lon = 16.363449;
    let apiUrl = `https://api.openweathermap.org/data/2.5/onecall?appid=${apiKey}&lat=${lat}&lon=${lon}&units=metric`;
    console.log(apiUrl);

    axios
      .get(apiUrl)
      .then((response) => {
        this.weather = response.data;
        console.log(this.weather);
      })
      .catch((e) => {
        this.error = e.message;
      });
  },
  computed: {
    iconUrl() {
      return `https://openweathermap.org/img/wn/${this.weather.current.weather[0].icon}@2x.png`
    },
    currentTemp() {
      return `${Math.round(this.weather.current.temp)} °C`
    },
    currentHum() {
      return `${this.weather.current.humidity} %`
    },
    counterTimesTwo() {
      return this.counter * 2
    },
    daily() {
      console.log(this.weather)
      return this.weather.daily
    }
  },
  methods: {
    addCounter() {
      this.counter++
    }
  }
};
</script>
