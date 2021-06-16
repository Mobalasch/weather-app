<template>
  <div class="hourContainer">
    <div class="hourHeader">
      <div class="hourFormat">
        {{ hourFormat }}
      </div>
      <div class="hourlyIcon"><img :src="iconUrlHour" /></div>
      <div class="hourlyTemp">{{ hourlyTemp }}</div>
      <button
        class="dropdown-open"
        @click="toggleDropdown"
        v-show="!dropdownOpen"
      >
        <i class="fa fa-caret-down" aria-hidden="true"></i>
      </button>
    </div>
    <div class="hourBody" v-show="dropdownOpen">
      <button class="dropdown-close" @click="toggleDropdown">
        <i class="fa fa-caret-up"></i>
      </button>
      <div class="hourlyWeather">{{ hour.weather[0].main }}</div>
      <div class="hourlyCloudy">Cloudy: {{ hourlyClouds }}</div>
      <div v-if="rain" class="rain">
        Chance of Rain {{ hourlyPop }}
        v-if="hour.rain" class="hourlyRain">Rain:
        {{ hourlyRain }}
      </div>
      <div class="hourlyWind">Wind: {{ hourlyWindSpeed }}</div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
export default {
  props: ["hour"],
  data() {
    return {
      rain: null,
      dropdownOpen: false,
    };
  },
  created() {
    console.log(this.hour);
  },
  computed: {
    iconUrlHour() {
      return `https://openweathermap.org/img/wn/${this.hour.weather[0].icon}@2x.png`;
    },
    hourlyTemp() {
      return `${Math.round(this.hour.temp)} °C`;
    },
    hourlyClouds() {
      return `${this.hour.clouds} %`;
    },
    hourlyWindSpeed() {
      return `${Math.round(this.hour.wind_speed * 3.6)} km/h`;
    },
    hourlyPop() {
      return `${Math.round(this.hour.pop * 100)} %`;
    },
    hourlyRain() {
      return `${this.hour.rain["1h"]} mm`;
    },
    hourFormat() {
      return moment.unix(this.hour.dt).format("HH:mm");
    },
  },
  methods: {
    toggleDropdown() {
      this.dropdownOpen = !this.dropdownOpen;
    },
  },
};
</script>

<style></style>
