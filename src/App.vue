<script>
import Player from './components/Player.vue'
import {Howl, Howler} from 'howler';

export default {
  name: 'app',
  components: {
    Player
  }
}
</script>

<template>
  <div id="app">
    <Player src="/dove.mp3" autoplay />
  </div>
</template>



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
