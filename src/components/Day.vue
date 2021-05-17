<template>
  <div>
    <h1>{{ day.dt }}</h1>
    <div>{{day.temp.min}} <i class="min fas fa-temperature-low"></i></div>
    <div>{{day.temp.max}} <i class="max fas fa-temperature-high"></i></div>
  </div>
</template>

<script>
export default {
  props: ['day']
}
</script>}

<style>
.max {
    color: red;
}
.min {
  color: blue;
}
</style>