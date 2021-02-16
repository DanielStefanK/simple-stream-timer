<template>
  <div
    @click.right="toggle"
    @click="onClickNumber"
    @click.middle="goBack"
    :class="{covercard: true, shakecard: currentSeconds <=0 && currentSeconds >-1 && isRunning}">
      <template v-if="!done">
        <h3 v-if="reps>0" class="removeSpace">{{repsDisplay}}</h3>
        <h1 :class="{alert: currentSeconds < 1 && state != 0, stopped: !isRunning, pulsing: currentSeconds <= -60 }">{{prettyTime}}</h1>
      </template>
      <template v-else>
        <h1 class="stopped">done</h1>
      </template>
  </div>
</template>

<script>
import { useRoute } from 'vue-router'
import { computed, ref } from 'vue'

const sound = require('@/assets/chime.mp3');

export default {
  name: 'App',
  setup () {
    var audio = new Audio(sound)
    const {intervals, autoChange, reps} = getConfig ()
    const isRunning = ref(false)
    const nextIntervalIndex = ref(0)
    let countdownInterval;

    const getInterval = () => {
      return intervals[nextIntervalIndex.value%intervals.length]
    }

    const currentRep = computed(()=>Math.floor ((nextIntervalIndex.value-1)/intervals.length) +1)
    const done = computed(() => reps > 0 && currentRep.value > reps)

    const currentSeconds = ref(getInterval())
    nextIntervalIndex.value++

    const play = () => {
      if (isRunning.value) return
      countdownInterval = setInterval(() => {
        currentSeconds.value--
        if (currentSeconds.value === 0) audio.play()

        if (currentSeconds.value === -1 && autoChange) {
          pause()
          loadNext ()
          if (!done.value)
            play()
        }
      }, 1000)
      isRunning.value = true
    }

    const pause = () => {
      if (!isRunning.value) return
      clearInterval(countdownInterval)
      isRunning.value = false
    }

    const toggle = (evnt) => {
      if (isRunning.value)
        pause()
      else
        play()
      if (evnt) evnt.preventDefault()
    }

    const loadNext = () => {
      if (done.value) return
      if (isRunning.value) pause()
      currentSeconds.value = getInterval()
      nextIntervalIndex.value++
    }

    const onClickNumber = () => {
      if (done.value) return
      if (isRunning.value) loadNext()
      else play()
    }

    const goBack = (evnt) => {
      if (isRunning.value) pause()
      if (nextIntervalIndex.value > 1)
        nextIntervalIndex.value -=2
      loadNext()
      if (evnt) evnt.preventDefault()
    }

    const prettyTime = computed(()=>{
      const seconds =  Math.abs(currentSeconds.value % 60)
      const minutes = Math.floor(Math.abs(currentSeconds.value / 60))
      return `${currentSeconds.value < 0?'-':''}${minutes > 9 ? '' : '0'}${minutes}:${seconds > 9 ? '' : '0'}${seconds}`
    })

    const repsDisplay = computed(()=>{
      return `${currentRep.value}/${reps}`
    })

    return {
      prettyTime,
      currentSeconds,
      isRunning,
      toggle,
      repsDisplay,
      onClickNumber,
      goBack,
      reps,
      done
    }
  }
}


const getConfig = () => {
    const route = useRoute()
    const intervals = route.query.intervals ? JSON.parse(route.query.intervals) : [25,5]
    const autoChange = route.query.autoChange ? JSON.parse(route.query.autoChange) : false
    const reps = route.query.reps ? JSON.parse(route.query.reps) : false

    return {
      intervals,
      autoChange,
      reps
    }
}
</script>

<style scoped>
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
  cursor: pointer;
}

.shakecard {
  /* Start the shake animation and make the animation last for 0.5 seconds */
  animation: shake 0.5s;

  /* When the animation is finished, start again */
  animation-iteration-count: once;
}

.pulsing {
  /* Start the shake animation and make the animation last for 0.5 seconds */
  animation: pulse 1s;

  /* When the animation is finished, start again */
  animation-iteration-count: infinite;
}

.removeSpace {
  position: fixed;
  transform: translate(135px, -15px);
}

@keyframes shake {
  10%, 90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%, 80% {
    transform: translate3d(2px, 0, 0);
  }

  30%, 50%, 70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%, 60% {
    transform: translate3d(4px, 0, 0);
  }
}


@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
  100%{
    transform: scale(1);
  }
}
</style>
