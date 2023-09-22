<template>
  <main>
    <div class="player">
       <video ref="video" width="1080" @click="videoPlayToggle()">
        <source src="./assets/test.mp4" type="video/mp4" />
      </video>

      <div class="controls">
        <button :class="[isPlaying ? 'pause-button' : 'play-button']" @click="videoPlayToggle()"></button>

        <div class="timer">
          <input class="" type="range" value="40" max="100">
          <span>{{ currentMinutes }}:{{ currentSeconds }}</span>
        </div>
        
        <div class="controls-to-right">
          <button :class="[ muted ? 'volume-button-no-audio': 'volume-button-audio']" @click="switchVolume()"></button>
          <div class="volume-control">
            <input class="volume" type="range" v-model="volumeLevel" min="0" max="1" step="0.01">
          </div> 

          <button class="menu-button"></button>
        </div>
      </div>
    </div>
  </main>
</template>

<!-- 
  Done TODO: 1.Usunąć animacje przy przycisku volume. Niech ten wskaźnik po prostu będzie statyczny.
  Done TODO: 2.Po kliknięciu w przycisk volume zmienia on ikonkę na przekroślony głośnik.
  Done TODO: 3.Głośność dźwięku działa i realnie wpływa na głośność video.

  TODO: 4.Zsynchronizować długość filmu z licznikiem oraz z inputem typu range.
  TODO: 5.Dodać przycisk ustawiający tryb pełnoekranowy dla video.
 -->

<script setup>
  import { ref, watch } from 'vue'

  const isPlaying = ref(false)
  const volumeLevel = ref('0.50')
  const muted = ref(false)
  const video = ref(null)
  const currentMinutes = ref('00')
  const currentSeconds = ref('00')


  const currentTime = () => {
    currentMinutes.value = Math.floor(video.value.currentTime / 60)
    currentSeconds.value = Math.floor(video.value.currentTime - currentMinutes.value * 60)

    if (currentSeconds.value < 10) {
      currentSeconds.value = `0${currentSeconds.value}`
    }

    if (currentMinutes.value < 10) {
      currentMinutes.value = `0${currentMinutes.value}`
    }
  }  

  watch(video, () => {
    video.value.addEventListener('timeupdate', currentTime)
  })

  const switchVolume = () => {
    if (volumeLevel.value == '0') {
      volumeLevel.value = '0.50'
    }
    else [
      volumeLevel.value = '0'
    ]
  }

  watch(volumeLevel, () => {
    if (volumeLevel.value == '0') {
      muted.value = true
      video.value.volume = volumeLevel.value;
    }
    else {
      muted.value = false
      video.value.volume = volumeLevel.value;
    }
  })

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

  .controls-to-right {
    display: flex;
    justify-content: right;
    text-align: right;
    width: 37%;
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
  .volume-button-audio {
    background-image: url('./assets/icons/audio-96.png');
    background-size: cover;
  }

  .volume-button-no-audio {
    background-image: url('./assets/icons/no-audio-96.png');
    background-size: cover;
  }

  .volume-control {
    line-height: 38px;
    width: 83px;
    opacity: 0.9;
    padding-left: 3px;
    padding-right: 5px;
    .volume {
      width: 100%;
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