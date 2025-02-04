<template>
    <div class="container">
        <div class="row">
            <WeatherComponent/>
            <NewsComponent/>
        </div>
    </div>
</template>

<script>
import WeatherComponent from './components/WeatherComponent.vue'
import NewsComponent from './components/NewsComponent.vue'

export default {
  name: 'App',
  components: {
    WeatherComponent,
    NewsComponent
  }
}
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
