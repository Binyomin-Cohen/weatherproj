<template>
  <div id="app">
    <!--<hello-world/>-->
    <weather-data/>
    <previous-searches/>

    <br><br>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld';
import WeatherData from './components/WeatherData';
import PreviousSearches from './components/PreviousSearches';
import HistoricChart from './components/HistoricChart';

export default {
  name: 'app',
  components: {
    HelloWorld, WeatherData, PreviousSearches, HistoricChart
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
