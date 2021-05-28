<template>
  <div class="hourlyWeather">
    <hr />
    <h2>{{ hourFormat }}</h2>
    <div class="hourlyTemp">Temperature: {{ hourlyTemp }}</div>
    <div class="hourlyIcon"><img :src="iconUrlHour" /></div>
    <div class="hourlyWeather">{{ hour.weather[0].main }}</div>
    <div class="hourlyCloudy">Cloudy: {{ hourlyClouds }}</div>
    <div class="hourlyPop">Chance of Rain {{ hourlyPop }}</div>
    <div v-if="hour.rain" class="hourlyRain">Rain: {{ hourlyRain }}</div>
    <div class="hourlyWind">Wind: {{ hourlyWindSpeed }}</div>
  </div>
</template>

<script>
import moment from "moment";
export default {
  props: ["hour"],

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
      return `${this.hour.pop * 100} %`;
    },
    hourlyRain() {
      return `${this.hour.rain["1h"]} mm`;
    },
    hourFormat() {
      return moment.unix(this.hour.dt).format("HH:mm");
    },
  },
};
</script>

<style></style>
