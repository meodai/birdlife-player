<script>
import Player from './components/Player.vue'


export default {
  name: 'app',
  components: {
    Player
  }
}
</script>

<template>
  <div id="app">
    <Player src="/nachtigall.mp3" title="Dis mami" autoplay controls/>
  </div>
</template>



<style>
h1, h2 ,h3 {
  all: unset;
  display: block;
  font-weight: bold;
  color: #fff;
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>
