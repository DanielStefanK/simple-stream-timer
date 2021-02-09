<template>
  <div class="covercard">
      <h1 @click="changeState" :class="{alert: currentTime < 1 && state != 0, stopped: state==0 }">{{prettyTime}}</h1>
  </div>
</template>

<script>
import { computed, ref } from 'vue'
const sound = require('@/assets/chime.mp3');

export default {
  name: 'App',
  setup () {
    const config = getConfig ()
    // 0 is stopped 1 is running
    const state = ref(0)
    const index = ref(0)
    const currentTime = ref (0)
    let countdownInterval;
    var audio = new Audio(sound)


    const changeState = ()=>{
      //is running
      if (state.value) {
        state.value = 0
        currentTime.value = config[index.value%config.length]
        clearInterval(countdownInterval)
      } else {
        state.value = 1
        const seconds = config[index.value%config.length]
        index.value++
        currentTime.value = seconds
        countdownInterval =  setInterval(()=>{
          currentTime.value--
          if (currentTime.value === 0) audio.play()
        }, 1000)
      }
    }

    const prettyTime = computed(()=>{
      const seconds =  Math.abs(currentTime.value % 60)
      const minutes = Math.abs(Math.[currentTime.value < 0 ?'round' : 'floor'](currentTime.value / 60))
      return `${currentTime.value<0?'-':''}${minutes>9?'':'0'}${minutes}:${seconds>9?'':'0'}${seconds}`
    })

    return {prettyTime, changeState, currentTime, state}
  }
}

const getConfig = () => {
    const urlParams = new URLSearchParams(window.location.search);
    return JSON.parse (urlParams.get('config'));
}
</script>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
  justify-items: center;
  color: #2c3e50;
}
h1 {
  font-size: 500%;
}
.alert {
  color: rgb(242, 115, 115);
}

.stopped {
  color: rgb(115, 230, 172);
}

.covercard {
  background-color: white;
  -webkit-box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
  -moz-box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
  box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
  width: 300px;
}
</style>
