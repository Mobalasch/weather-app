<template>
  <div class="dayContainer">
    <div class="dayFormat">
      {{ dayFormat }}
    </div>
    <div class="dailyIcon"><img :src="iconUrlDay" /></div>
    <div class="tempMin">
      {{ Math.round(day.temp.min) }} <i class="min fas fa-temperature-low"></i>
    </div>
    <div class="tempMax">
      {{ Math.round(day.temp.max) }} <i class="max fas fa-temperature-high"></i>
    </div>
  </div>
</template>

<script>
import moment from "moment";
export default {
  props: ["day"],
  computed: {
    dayFormat() {
      return moment
        .unix(this.day.dt)
        .utc()
        .format("dddd DD.MM.YYYY");
    },
    iconUrlDay() {
      return `https://openweathermap.org/img/wn/${this.day.weather[0].icon}@2x.png`;
    },
  },
};
</script>

<style></style>
