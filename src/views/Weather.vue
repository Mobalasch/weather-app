<template>
  <div v-if="weather" class="weather">
    <h1>Weather App</h1>
    <div v-if="error">{{ error }}</div>
    <div class="current">
      <!--  <div class="location">{{ "Location: " + weather.timezone }}</div> -->
     <!--  <div class="location">{{ cityName }}</div> -->
      <div class="cur-date">{{ momentUnix }}</div>
      <div>
        {{ Math.round(currentDay.temp.min) }}
        <i class="min fas fa-temperature-low"></i>
      </div>
      <div>
        {{ Math.round(currentDay.temp.max) }}
        <i class="max fas fa-temperature-high"></i>
      </div>
      <div class="weather-desc">{{ weather.current.weather[0].main }}</div>
      <div class="icon"><img :src="iconUrl" /></div>
      <div class="temp">Temperature: {{ currentTemp }}</div>
      <div class="hum">Humidity: {{ currentHum }}</div>
      <div class="cloudy">Cloudy: {{ currentClouds }}</div>
      <div v-if="this.weather.rain" class="rain">Rain: {{ currentRain }}</div>
      <div class="wind-speed">Wind: {{ currentWindSpeed }}</div>
      <TemperatureChart :hourly="hourly" />
    </div>
    <div class="hourly"></div>
    <!-- Hourly Komponente mit z.B. Grafik für Temperatur und Niederschlag oder Side Scrolling -->
    <div v-for="hour in hourly" :key="hour.dt">
      <Hour :hour="hour" />
    </div>
    <div class="daily"></div>
    <!-- Daily -->

    <!-- Day Components -->
    <div v-for="day in daily" :key="day.dt">
      <Day :day="day" />
    </div>

    <!-- 
    <div v-for="(day, index) in daily" :key="index">
    </div>
    -->
  </div>
</template>

<script>
import Day from "@/components/Day";
import Hour from "@/components/Hour";
import TemperatureChart from "@/components/TemperatureChart";
import axios from "axios";
import moment from "moment";

export default {
  name: "Home",
  components: {
    Day,
    Hour,
    TemperatureChart,
  },
  data() {
    return {
      location: null,
      weather: null,
      error: null,
    };
  },

  created() {
    navigator.geolocation.getCurrentPosition((position) => {
      let lat = position.coords.latitude;
      let lon = position.coords.longitude;
      console.log(position); //position.coords.latitude, position.coords.longitude
      // Methode zur Abfrage der Wetterdaten verwenden
      this.getWeatherData(lat, lon);
      // Name der die Funktion beschreibt
      // this.getLocaleCityName oder this.getCurrentCityName ...
      this.getCurrentCityName(lat,lon);
    });
  },
  computed: {
    cityName() {
      return this.location.components.city;
    },
    iconUrl() {
      return `https://openweathermap.org/img/wn/${this.weather.current.weather[0].icon}@2x.png`;
    },
    currentTemp() {
      return `${Math.round(this.weather.current.temp)} °C`;
    },
    currentDay() {
      return this.weather.daily[0];
    },
    currentHum() {
      return `${this.weather.current.humidity} %`;
    },
    currentClouds() {
      return `${this.weather.current.clouds} %`;
    },
    currentRain() {
      return `${this.weather.current.rain[0]["1h"]} mm`;
    },
    currentWindSpeed() {
      return `${Math.round(this.weather.current.wind_speed * 3.6)} km/h`;
    },
    daily() {
      return this.weather.daily;
    },
    hourly() {
      return this.weather.hourly;
    },
    currentDt() {
      return this.weather.current.dt;
    },
    currentTimezone() {
      return this.weather.timezone;
    },
    momentUnix() {
      // https://stackoverflow.com/questions/20943089/how-to-convert-unix-timestamp-to-calendar-date-moment-js/20943421
      // return moment.unix.currentDt.utc.add(currentTimezone, 's')
      // https://momentjscom.readthedocs.io/en/latest/moment/04-displaying/01-format/
      return moment.unix(this.currentDt).format("dddd DD.MM.YYYY HH:mm");
    },
  },
  methods: {
    getWeatherData(lat, lon) {
      // Api URL zusammensetzen
      // Wetterdaten abfragen
      // Wetterdaten in this.weather zurückgeben
      const apiKey = process.env.VUE_APP_WEATHER_API_KEY;
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
   /*  getCurrentCityName(lat, lon) {
      const geoApiKey = process.env.VUE_APP_GEO_API_KEY;
      // let geoApiUrl = `https://api.opencagedata.com/geocode/v1/json?q=LAT+LNG&key=b8a5934b13ce40bd97503cd457e1b5b8`;
      let geoApiUrl = `https://api.opencagedata.com/geocode/v1/json?q=LAT=${lat}&LNG=${lon}&key=${geoApiKey}`;
      console.log(geoApiUrl);

      axios
        .get(geoApiUrl)
        .then((response) => {
          this.location = response.data;
          console.log(this.location);
        })
        .catch((e) => {
          this.error = e.message;
        });
    }, */
  },
};
</script>
