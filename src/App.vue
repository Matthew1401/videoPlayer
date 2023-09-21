<script setup>
  import { ref } from 'vue'

  const isPlaying = ref(false)
  const video = ref(null)
  const volumeControl = ref(null)
  const volume = ref(null)
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

  let isClicked = true
  const showVolumeLevel = () => {
    if (isClicked) {
      volumeControl.value.style.width = '85px'
      volume.value.style.width = '100%'
      volume.value.style.opacity = '1';
      console.log(volume.value.style)
      isClicked = false;
    }
    else {
      volumeControl.value.style.width = '0'
      volume.value.style.width = '0'
      volume.value.style.opacity = '0';
      console.log(volume.value.style)
      isClicked = true;
    }
    
  }

</script>

<template>
  <main>
    <div class="player">
       <video ref="video" width="1080" @click="videoPlayToggle()">
        <source src="./assets/test.mp4" type="video/mp4" />
      </video>

      <div class="controls">
        <button :class="[isPlaying ? 'pause-button' : 'play-button']" @click="videoPlayToggle()"></button>

        <div class="timer">
          <input class="" type="range" value="0" max="100">
          <span aria-label="timer">00:00</span>
        </div>
        
          <button class="volume-button" @click="showVolumeLevel()"></button>
          <div ref="volumeControl" class="volume-control">
            <input ref="volume" class="volume" type="range" value="50" max="100">
          </div> 

          <button class="menu-button"></button>
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
    opacity: 1;
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

  /* PLAY BUTTON TO SWITCH ON OR OFF THE VIDEO */
  .play-button {
    background-image: url('./assets/icons/play-96.png');
    background-size: cover;
  }

  .pause-button {
    background-image: url('./assets/icons/pause-96.png');
    background-size: cover;
  }

  /* VOLUME BUTTON TO SHOW AND TO MANIPULATE THE VOLUME LEVEL */
  .volume-button {
    background-image: url('./assets/icons/speaker-96.png');
    background-size: cover;
  }

  .volume-control {
    transition: all 1s;
    line-height: 38px;
    width: 0px;
    opacity: 0.9;
    padding-left: 3px;
    padding-right: 5px;
    .volume {
      display: block;
      opacity: 0;
      transition: all 1s;
      width: 0%;
      height: 38px;
    }
  }

  .menu-button {
    background-image: url('./assets/icons/menu-100.png');
    background-size: cover;
  }

  .timer {
    line-height: 38px;
    font-size: 10px;
    font-family: monospace;
    text-shadow: 1px 1px 0px black;
    color: white;
    position: relative;
    /* background-color: gray; */
    width: 500px;
  }

  .timer input {
    position: absolute;
    background-color: rgba(255, 255, 255, 0.2);
    left: 70px;
    top: 0;
    width: 420px;
    height: 38px;
    z-index: 2;
  }

  .timer span {
    position: absolute;
    z-index: 3;
    left: 19px;
    font-size: 1rem;
  }
</style>