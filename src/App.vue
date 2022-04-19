<template>
  <ArWeave />
</template>

<script>
import ArWeave from "./components/ArWeave.vue";

export default {
  name: "App",
  components: {
    ArWeave,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
