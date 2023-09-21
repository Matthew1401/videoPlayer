<script setup>
  import { ref, onMounted } from 'vue'

  const isPlaying = ref(false)
  const video = ref(null)
  const currentVideoTime = ref(0)

  const videoPlayToggle = () => {
    if (video.value.paused) {
      video.value.play()
      isPlaying.value = true;
      return
    }
    else {
      video.value.pause()
      isPlaying.value = false;
      return
    }
  }

</script>

<template>
  <main>
    <div class="player">
       <video ref="video" width="1080" @click="videoPlayToggle()">
        <source src="./assets/earth_1920.mp4" type="video/mp4" />
      </video>

      <div class="controls">
        <button :class="[isPlaying ? 'pause-button' : 'play-button']" aria-label="play pause toggle" @click="videoPlayToggle()"></button>
        <button class="stop-button" data-icon="S" aria-label="stop" @click=""></button>

        <div class="timer">
          <div></div>
          <span aria-label="timer">00:00</span>
        </div>

          <button class="rwd" data-icon="B" aria-label="rewind"></button>
          <button class="fwd" data-icon="F" aria-label="fast forward"></button>
        </div>
    </div>
  </main>
</template>

<style scoped>
  main {
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: gray;
  }

  .player {
    height: auto;
    background-color: rgb(89, 204, 123);
    padding: 3px;
    position: relative;
  }

  .player:hover .controls,
  .player:focus-within .controls {
    opacity: 1;
  }

  .controls {
    opacity: 0;
    width: 80%;
    border-radius: 10px;
    padding: 5px 10px;
    position: absolute;
    bottom: 20px;
    margin-left: 10%;
    margin-right: 15px;
    background-color: black;
    box-shadow: 3px 3px 5px black;
    transition: opacity 1s;
    display: flex;
  }

  button {
    margin: 2px;
    border: none;
    border-radius: 15px;
    height: 34px;
    width: 34px;
    cursor: pointer;
  }

  button:hover {
    background-color: gray;
  }

  .play-button {
    background-image: url('./assets/icons/play-96.png');
    background-size: cover;
  }

  .pause-button {
    background-image: url('./assets/icons/pause-96.png');
    background-size: cover;
  }

  .timer {
    line-height: 38px;
    font-size: 10px;
    font-family: monospace;
    text-shadow: 1px 1px 0px black;
    color: white;
    flex: 5;
    position: relative;
  }

  .timer div {
    position: absolute;
    background-color: rgba(255, 255, 255, 0.2);
    left: 0;
    top: 0;
    width: 0;
    height: 38px;
    z-index: 2;
  }

  .timer span {
    position: absolute;
    z-index: 3;
    left: 19px;
  }
</style>