<template>
  <Navigation />
  <router-view />
</template>

<script>
import Navigation from "@/components/Navigation";

export default {
  components: {
    Navigation
  },
};
</script>

<style>
#app {
  font-family: Roboto, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
}
</style>
