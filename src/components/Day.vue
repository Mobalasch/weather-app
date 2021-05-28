<template>
  <div>
    <h2>{{ dayFormat }}</h2>
    <div>{{ day.temp.min }} <i class="fas fa-temperature-low"></i></div>
    <div>{{ day.temp.max }} <i class="fas fa-temperature-high"></i></div>
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
  },
};
</script>

<style></style>
