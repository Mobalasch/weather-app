<template>
  <div v-if="weather" class="weather">
    <h1>Weather App</h1>
    <div v-if="location">
      <label for="search">Location Search: </label>
      <input
        type="text"
        name="location-search"
        :placeholder="cityName"
        v-model="placeInput"
      />
      <br />
      <button class="button-search" @click="getSearchedCityName(placeInput)">
        Search
      </button>
    </div>
    <div v-if="error">{{ error }}</div>
    <div class="currentHeader">
      <div class="current">
        <div class="location" v-if="location">{{ cityName }}</div>
        <div class="cur-date">{{ momentUnix }}</div>
        <div class="temp">Temperature: {{ currentTemp }}</div>
        <div>
          {{ Math.round(currentDay.temp.min) }}
          <i class="min fas fa-temperature-low"></i>
        </div>
        <div>
          {{ Math.round(currentDay.temp.max) }}
          <i class="max fas fa-temperature-high"></i>
        </div>
        <div class="icon"><img :src="iconUrl" /></div>
        <button
          class="dropdown-open"
          @click="toggleDropdown"
          v-show="!dropdownOpen"
        >
          <i class="fa fa-caret-down" aria-hidden="true"></i>
        </button>
      </div>
      <div class="currentBody" v-show="dropdownOpen">
        <button class="dropdown-close" @click="toggleDropdown">
          <i class="fa fa-caret-up"></i>
        </button>
        <div class="weather-desc">{{ weather.current.weather[0].main }}</div>
        <div class="hum">Humidity: {{ currentHum }}</div>
        <div class="cloudy">Cloudy: {{ currentClouds }}</div>
        <div v-if="this.weather.rain" class="rain">Rain: {{ currentRain }}</div>
        <div class="wind-speed">Wind: {{ currentWindSpeed }}</div>
      </div>
      <div class="temperatureChart">
        <TemperatureChart :hourly="hourly" />
      </div>
    </div>
    <!-- Hourly Komponente mit z.B. Grafik für Temperatur und Niederschlag oder Side Scrolling -->
    <div class="hourly">
      <Hour v-for="hour in hourly" :key="hour.dt" :hour="hour" />
    </div>
    <!-- Daily -->
    <div class="daily">
      <Day v-for="day in daily" :key="day.dt" :day="day" />
    </div>
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
      placeInput: null,
      dropdownOpen: false,
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
      this.getCurrentCityName(lat, lon);
    });
  },
  computed: {
    cityName() {
      const components = this.location.results[0].components;

      //  city, town, township, village
      if (components.city) {
        return components.city;
      } else if (components.town) {
        return components.town;
      } else if (components.township) {
        return components.township;
      } else {
        return components.village;
      }
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
    toggleDropdown() {
      this.dropdownOpen = !this.dropdownOpen;
    },
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
    getCurrentCityName(lat, lon) {
      const geoApiKey = process.env.VUE_APP_GEO_API_KEY;
      //reverse geoCoding
      // let geoApiUrl = `https://api.opencagedata.com/geocode/v1/json?q=LAT+LNG&key=b8a5934b13ce40bd97503cd457e1b5b8`;
      let geoReverseApiUrl = `https://api.opencagedata.com/geocode/v1/json?q=${lat}+${lon}&key=${geoApiKey}`;
      console.log(geoReverseApiUrl);

      axios
        .get(geoReverseApiUrl)
        .then((response) => {
          this.location = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          this.error = e.message;
        });
    },
    // Location Search
    getSearchedCityName(placeInput) {
      const geoApiKey = process.env.VUE_APP_GEO_API_KEY;

      // forward geoCoding
      let geoForwardApiUrl = `https://api.opencagedata.com/geocode/v1/json?q=${placeInput}&key=${geoApiKey}`;

      axios
        .get(geoForwardApiUrl)
        .then((response) => {
          this.location = response.data;
          const geoApiLat = this.location.results[0].geometry.lat;
          const geoApiLon = this.location.results[0].geometry.lng;
          this.getWeatherData(geoApiLat, geoApiLon);
        })
        .catch((e) => {
          this.error = e.message;
        });
      this.placeInput = "";
    },
  },
};
</script>
