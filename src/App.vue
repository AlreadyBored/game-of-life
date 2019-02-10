<template>
  <div id="app">
    <Playfield></Playfield>
  </div>
</template>

<script>
import Playfield from './components/Playfield.vue'

export default {
  name: 'app',
  components: {
    Playfield
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
