<script>
import {Howl, Howler} from 'howler';

export default {
  name: 'Player',
  props: {
    src: String,
    autoplay: Boolean
  },
  data: () => ({
    
  }),
  mounted () {
    this.sound = new Howl({
      src: [this.src]
    });

    if (this.autoplay) {
      this.sound.play();
    }

    this.analyser = Howler.ctx.createAnalyser();
    Howler.masterGain.connect(this.analyser);
    this.analyser.connect(Howler.ctx.destination);
    this.analyser.fftSize = 2048;
    var bufferLength = this.analyser.frequencyBinCount;
    var dataArray = new Uint8Array(bufferLength);
    this.analyser.getByteTimeDomainData(dataArray);
    this.canvasCtx = this.$refs.canvas.getContext('2d');
    this.canvasCtx.clearRect(0, 0, 400, 200);

    const draw = () => {
      this.drawVisual = requestAnimationFrame(draw);

      this.analyser.getByteFrequencyData(dataArray);

      this.canvasCtx.fillStyle = 'rgb(0, 0, 0)';
      this.canvasCtx.fillRect(0, 0, 400, 200);
      var barWidth = (200 / bufferLength) * 2.5;
      var barHeight;
      var x = 0;
      for(var i = 0; i < bufferLength; i++) {
        barHeight = dataArray[i];

        this.canvasCtx.fillStyle = 'hsl(' + (barHeight - 300) + ',100%,50%)';
        this.canvasCtx.fillRect(x,200-barHeight/2,barWidth,barHeight);

        x += barWidth + 1;
      }
    };

    draw()
  }
}
</script>

<template>
  <div class="player">
    <canvas class="canvas" ref="canvas"></canvas>
  </div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
