<template>
  <div class="home">
    <h1>Home</h1>
    <div v-if="error">{{error}}</div>
    <div v-if="weather">{{weather.current.weather[0].description}}</div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'

export default {
  name: "Home",
  components: {},
  data() {
    return {
      weather: null,
      error: null
    }
  },

  created() {
    const apiKey = process.env.VUE_APP_WEATHER_API_KEY;
    let lat = 48.210033;
    let lon = 16.363449;
    let apiUrl =`https://api.openweathermap.org/data/2.5/onecall?appid=${apiKey}&lat=${lat}&lon=${lon}`;
    console.log(apiUrl);

    axios.get(apiUrl)
    .then(response => {
      this.weather = response.data;
      console.log(this.weather);
    })
    .catch(e => {
      this.error = e.message;
    })
  } 
};
</script>
