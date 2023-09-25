<template>
  <main>
    <div class="player">
       <video ref="video" width="1080" @click="videoPlayToggle()">
        <source src="./assets/test.mp4" type="video/mp4" />
      </video>

      <div class="controls">
        <button :class="[isPlaying ? 'pause-button' : 'play-button']" @click="videoPlayToggle()"></button>

        <div class="timer">
          <span class="current">{{ currentMinutes }}:{{ currentSeconds }}</span>
          <div class="space-for-progress-bar">
            <div ref="progress" class="progress-bar" onmousedown="">
              <div ref="progressBar" class="progress-bar-fullfiled"></div>
            </div>
          </div>
          <span class="duration">{{ durationMinutes }}:{{ durationSeconds }}</span>
        </div>
        
        <div class="controls-to-right">
          <button :class="[ muted ? 'volume-button-no-audio': 'volume-button-audio']" @click="switchVolume()"></button>
          <div class="volume-control">
            <input class="slider" type="range" v-model="volumeLevel" min="0" max="1" step="0.01">
          </div> 

          <button class="fullscreen-button" @click="fullscreen()"></button>
          <button class="menu-button" @click="showSelection = ! showSelection"></button>
          <select class="video-speed" :class="[showSelection ? '': 'hide']" multiple>
            <option @click="changePlaybackRate(0.25)">0.25</option>
            <option @click="changePlaybackRate(0.5)">0.50</option>
            <option @click="changePlaybackRate(0.75)">0.75</option>
            <option selected @click="changePlaybackRate(1)">Normalna</option>
            <option @click="changePlaybackRate(1.25)">1.25</option>
            <option @click="changePlaybackRate(1.5)">1.50</option>
            <option @click="changePlaybackRate(1.75)">1.75</option>
          </select>
        </div>
      </div>
    </div>
  </main>
</template>

<!-- 
  Done TODO: 1.Usunąć animacje przy przycisku volume. Niech ten wskaźnik po prostu będzie statyczny.
  Done TODO: 2.Po kliknięciu w przycisk volume zmienia on ikonkę na przekroślony głośnik.
  Done TODO: 3.Głośność dźwięku działa i realnie wpływa na głośność wideo.
  Done TODO: 4.Zsynchronizować długość filmu z licznikiem oraz z inputem typu range.
  Done TODO: 5.Dodać przycisk ustawiający tryb pełnoekranowy dla wideo.
  Done TODO: 6.Zmienić stylizację input volume range.
  Done TODO: 7.Dodać możliwość przesuwania postępu wideo za pomocą progress bar.
  Done TODO: 8.Dodać możliwość przesuwania filmu o 15 sekund do przodu i do tyłu za pomocą strzałek.
  Done TODO: 9.Gdy ustawienia będą zmieniane w trybie fullscreen dodać aktualizacje wyglądu przycisków oraz volume.
  Done TODO: 10.Dodać całkowity czas pliku wideo.
  Done TODO: 11.Dodać wybór prędkości odtwarzania wideo. Najlepiej wybor select i option przy przycisku menu.

  TODO: 12.Dodać rezponsywność do strony.
-->

<script setup>
  import { ref, watch, onMounted } from 'vue'

  const isPlaying = ref(false)
  const volumeLevel = ref('0.50')
  const muted = ref(false)
  const video = ref(null)
  const currentMinutes = ref('00')
  const currentSeconds = ref('00')
  const durationMinutes = ref('00')
  const durationSeconds = ref('00')
  const progress = ref(null)
  const progressBar = ref(null)
  const showSelection = ref(false)


  onMounted(() => {
    // UPDATING TIME
    video.value.addEventListener('timeupdate', currentTime)

    // UPDATING CURRENT VIDEO TIME USING PROCESS BAR
    progress.value.addEventListener('click', (e) => {
      const progressTime = (e.offsetX / progress.value.offsetWidth) * video.value.duration
      video.value.currentTime = progressTime
    })

    // UPDATE CURRENT VIDEO TIME USING ARROWS
    window.addEventListener('keydown', (e) => {
      let letter = String.fromCharCode(e.keyCode)
      if (letter == "'") {
        video.value.currentTime = video.value.currentTime + 15
      }
      if (letter == "%") {
        video.value.currentTime = video.value.currentTime - 15
      }
      if (letter == " ") {
        videoPlayToggle()
      }
    })
    
   video.value.addEventListener("ended", () => {
      console.log("Offline audio processing now complete");
      video.value.pause()
      isPlaying.value = false
    })

    video.value.addEventListener("pause", () => {
      isPlaying.value = false
    })

    video.value.addEventListener("play", () => {
      isPlaying.value = true
    })

    video.value.addEventListener("volumechange", () => {
      volumeLevel.value = `${video.value.volume}`
    })

    video.value.addEventListener("muted", () => {
      console.log('Muted')
    })
  })

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

  const changePlaybackRate = (rate) => {
    video.value.playbackRate = rate
    showSelection.value = false
  }

  const currentTime = () => {
    durationTime()

    // UPDATING THE TIMER VALUE
    currentMinutes.value = Math.floor(video.value.currentTime / 60)
    currentSeconds.value = Math.floor(video.value.currentTime - currentMinutes.value * 60)

    if (currentSeconds.value < 10) {
      currentSeconds.value = `0${currentSeconds.value}`
    }

    if (currentMinutes.value < 10) {
      currentMinutes.value = `0${currentMinutes.value}`
    }

    // UPDATING THE BAR PROGRESS
    const percentage = (video.value.currentTime / video.value.duration) * 100
    progressBar.value.style.width = `${percentage}%`
  }  

  const durationTime = () => {
    durationMinutes.value = Math.floor(video.value.duration / 60)
    durationSeconds.value = Math.floor(video.value.duration - durationMinutes.value * 60)

    if (durationSeconds.value < 10) {
        durationSeconds.value = `0${durationSeconds.value}`
      }

      if (durationMinutes.value < 10) {
        durationMinutes.value = `0${durationMinutes.value}`
      }
  }

  const switchVolume = () => {
    if (volumeLevel.value == '0') {
      volumeLevel.value = '0.50'
    }
    else [
      volumeLevel.value = '0'
    ]
  }

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

  const fullscreen = () => {
    video.value.requestFullscreen()
  }

</script>


<style scoped>
  main {
    width: 100vw;
    height: 100vh;
    font-size: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .player {
    height: auto;
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
    width: 35%;
    float: right;
  }

  button {
    margin: 2px;
    border: none;
    border-radius: 15px;
    height: 2.5em;
    width: 2.5em;
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
    width: 5em;
    opacity: 0.9;
    padding-left: 3px;
    padding-right: 5px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .slider {
    background-color: rgba(192, 12, 12, 0.2);
    border: 1px solid rgba(192, 12, 12, 0.2);
    border-radius: 5px;
    cursor: pointer;
    opacity: 0.7;
    transition: opacity 2s;
    width: 100%;
    height: 0.9em;
    -webkit-appearance: none;
    appearance: none;
    outline: none;
    -webkit-transition: .2s
  }

  .slider:hover {
    opacity: 1;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 1em;
    height: 1em;
    background: orangered;
    border-radius: 50%;
    cursor: pointer;
  }

  .slider::-moz-range-thumb {
    width: 1em;
    height: 1em;
    background: orangered;
    cursor: pointer;
  }

  /* MENU AND FULLSCREEN BUTTONS */
  .menu-button {
    background-image: url('./assets/icons/menu-100.png');
    background-size: cover;
  }

  .fullscreen-button {
    background-image: url('./assets/icons/fullscreen.png');
    background-size: cover;  
  }

  /* TIMER AND PROGRESS BAR */
  .timer {
    line-height: 2.4em;
    font-size: 0.8em;
    font-family: monospace;
    text-shadow: 1px 1px 0px black;
    color: white;
    position: relative;
    width: 60%;
  }

  .space-for-progress-bar {
    float: right;
    width: 87%;
    height: 100%;
    display: flex;
    justify-content: left;
    align-items: center;
  }

  .progress-bar {
    background-color: rgba(192, 12, 12, 0.2);
    width: 100%;
    height: 0.9em;
    border: 1px solid rgba(192, 12, 12, 0.2);
    border-radius: 5px;
    cursor: pointer;
    opacity: 0.7;
    transition: opacity 2s;
  }

  .progress-bar:hover {
    opacity: 1;
  }

  .progress-bar-fullfiled {
    background-color: orangered;
    width: 0%;
    height: 100%;
    border-radius: 5px;
  }

  .timer .current {
    position: absolute;
    left: 0.8em;
  }

  .timer .duration {
    position: absolute;
    right: -3.5em;
  }

  .video-speed {
    position: absolute;
    height: 9em;
    width: 5em;
    top: -9em;
    right: -0;
    outline: none;
    text-align: center;
    border-radius: 5px;
    font-family: monospace;
    text-shadow: 1px 1px 0px black;
    color: white;
    font-size: 0.8em;
    background-color: black;
  }

  .video-speed option:hover {
    background-color: orangered;
  }

  .video-speed option:checked {
    background-color: orangered;
  }

  .video-speed::-webkit-scrollbar {
    display: none;
  }

  .hide {
    display: none;
  }

  /* MEDIA SCREEN 1200PX */
  @media only screen and (max-width: 1200px) {
    video {
      width: 640px;
    }

    button {
      height: 1.8em;
      width: 1.8em;
    }

    .volume-control {
      width: 4em;
    }

    .slider {
      height: 0.8em;
    }

    .slider::-webkit-slider-thumb {
      width: 0.8em;
      height: 0.8em;
    }

    .slider::-moz-range-thumb {
      width: 0.8em;
      height: 0.8em;
    }

    .timer {
      font-size: 0.6em;
    }

    .timer .current {
      position: absolute;
      left: 0.4em;
    }

    .timer .duration {
      position: absolute;
      right: -3.5em;
    }

     .controls-to-right {
      width: 49%;
    }

    .space-for-progress-bar {
      width: 80%;
    }

    .video-speed {
      width: 5em;
      font-size: 0.6em;
    }
  }

  /* MEDIA SCREEN 720PX */
  @media only screen and (max-width: 720px) {
    video {
      width: 480px;
    }

    button {
      height: 1.5em;
      width: 1.5em;
    }

    .volume-control {
      width: 2.4em;
    }

    .slider {
      height: 0.7em;
    }

    .slider::-webkit-slider-thumb {
      width: 0.7em;
      height: 0.7em;
    }

    .slider::-moz-range-thumb {
      width: 0.7em;
      height: 0.7em;
    }

    .timer {
      font-size: 0.5em;
    }

    .timer .current {
      position: absolute;
      left: 0.4em;
    }

    .timer .duration {
      position: absolute;
      right: -3.5em;
    }

    .controls-to-right {
      width: 50%;
    }

    .space-for-progress-bar {
      width: 80%;
    }

    .video-speed {
      width: 5em;
      font-size: 0.5em;
    }
  }

</style>