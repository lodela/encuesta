<template>
  <div id="app">
    <Encuesta msg="Encuestas"/>
  </div>
</template>

<script>
import Encuesta from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    Encuesta
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
