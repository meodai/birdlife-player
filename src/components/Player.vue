<script>
import {Howl, Howler} from 'howler';

export default {
  name: 'Player',
  props: {
    src: String,
    autoplay: Boolean,
    controls: Boolean,
    loop: Boolean,
    volume: Number,
    title: String
  },
  data: () => ({
    
  }),
  mounted () {
    this.sound = new Howl({
      src: [this.src],
      autoplay: this.autoplay || false,
      loop: this.loop || false,
      volume: this.volume || 1
    });

    this.sound.onpause
    this.sound.onstop

    if (this.autoplay) {
      this.sound.play();
    }

    this.analyser = Howler.ctx.createAnalyser();
    Howler.masterGain.connect(this.analyser);
    
    this.analyser.connect(Howler.ctx.destination);
    this.analyser.fftSize = 2048;

    /*this.decRange = [-80.0, 80.0];
    
    this.analyser.minDecibels = this.decRange[0];
    this.analyser.maxDecibels = this.decRange[1];*/
    this.analyser.smoothingTimeConstant = 0.0;

    var bufferLength = this.analyser.frequencyBinCount;
    var dataArray = new Uint8Array(bufferLength);
    this.analyser.getByteTimeDomainData(dataArray);
    

    this.canvasCtx =  this.setupCanvas();
    this.canvasCtx.clearRect(0, 0, 400, 200);
    this.convasRect = this.$refs.canvas.getBoundingClientRect();
    /* draw bars */
    /*
    const draw = () => {
      this.drawVisual = requestAnimationFrame(draw);
      this.analyser.getByteFrequencyData(dataArray);

      this.canvasCtx.fillStyle = 'rgb(255, 255, 255)';
      this.canvasCtx.fillRect(0, 0, 400, 200);
      const barWidth = (200 / bufferLength) * 2.5;
      let barHeight;
      var x = 0;
      for(let i = 0; i < bufferLength; i++) {
        barHeight = dataArray[i];
        this.canvasCtx.fillStyle = `hsl(360,0%,${100 - (barHeight/200 * 100)}%)`;
        this.canvasCtx.fillRect(x,200-barHeight/2,barWidth,barHeight);

        x += barWidth + 1;
      }
    };
    */
    
    /* draw sonogram */
    var scriptNode = Howler.ctx.createScriptProcessor(this.analyser.fftSize, 1, 1);
    scriptNode.onaudioprocess = () => {
      dataArray = new Uint8Array(this.analyser.frequencyBinCount);
      this.analyser.getByteFrequencyData(dataArray);
      console.log(dataArray)
    }
    
    const draw = () => {
      this.drawVisual = requestAnimationFrame(draw);
      this.analyser.getByteFrequencyData(dataArray);
      let canvas = this.$refs.canvas;
      this.canvasCtx.fillStyle = `#000`;
      for (let i = 0; i < dataArray.length; i++) {
        //console.log(dataArray)
        let value = dataArray[i];
        this.canvasCtx.fillStyle = `hsl(360,0%,${value/128 * 100}%)`;
        this.canvasCtx.fillRect(this.convasRect.width - 1, this.convasRect.height - i, 1, 1);
      }
      
    }
    draw();
  },

  methods: {
    togglePlay: function () {
      this.sound.play();
      this.sound.pause();
    },
    setupCanvas: function () {
      const dpr = window.devicePixelRatio || 1;
      // Get the size of the canvas in CSS pixels.
      const rect = this.$refs.canvas.getBoundingClientRect();
      // Give the canvas pixel dimensions of their CSS
      // size * the device pixel ratio.
      this.$refs.canvas.width = rect.width * dpr;
      this.$refs.canvas.height = rect.height * dpr;
      
      const ctx = this.$refs.canvas.getContext('2d');
      // Scale all drawing operations by the dpr, so you
      // don't have to worry about the difference.
      ctx.scale(dpr, dpr);
      return ctx;
    }
  }
}
</script>

<template>
  <article class="player">
    <h3 v-if="title" class="player__title">{{title}}</h3>
    <div class="player__inner">
      <div v-if="controls" class="controls">
        <button @click="togglePlay">
          <span>Play</span>
        </button>
      </div>
      <canvas class="canvas" ref="canvas"></canvas>
    </div>
  </article>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.player {
  background: #212121;
  padding: 4% 2%;
}

.player__inner {
  display: flex;
}

.player__title {
  margin-top: -2%;
  margin-bottom: 2%;
}

a {
  color: #fff;
}
.controls {
  width: 11%;
  margin-right: 2%;
}

button {
  position: relative;
  display: block;
  background: #fff;
  border: none;
  appearance: none;
  width: 100%;
  padding: 100% 0 0 0;
  border-radius: 50%;
  box-shadow: inset 0 0 0 .15rem #fff,
              inset 0 0 0 .2rem #212121;
}

button::after, 
button::before {
  content: "";
  position: absolute;
  top: 50%; 
  display: block;
  background: #212121;
  width: 10%; height: 45%;
  transform: translate3d(-50%, -50%, 0);
  transition: 300ms transform ease-in, 300ms height ease-in;
  height: 39.5%;
}
button::before {
  left: calc(50%);
  transform: translate3d(-175%, -50%, 0);
}
button::after {
  left: calc(50%);
  transform: translate3d(75%, -50%, 0);
}
button span {
  display: none;
}

.canvas {
  width: 100%;
  height: 10rem;
  background-color: #fff;
}

</style>
