<script>
import { ref } from 'vue'

let intervalID

export default {
  mounted() {
    this.startSeconds = Math.floor(Date.now() / 1000)
    intervalID = setInterval(this.handleIntervalTick, 250)
  },

  data() {
    return {
      startSeconds: 0,
      currentStep: 0,
      countDownValue: "",
      isRunning: true,
      blindSchedule: [
        { step: 1, minutes: 0.25, smallBlind: 50, bigBlind: 100},
        { step: 2, minutes: 0.25, smallBlind: 100, bigBlind: 200},
        { step: 3, minutes: 0.5, smallBlind: 150, bigBlind: 300},
        { step: 4, minutes: 10, smallBlind: 200, bigBlind: 400}
      ]
    }
  },

  methods: {
    handleIntervalTick() {
        if (this.isRunning) {
          let display = this.getDisplayTimer()
          this.countDownValue = `${display.minutes}:${display.seconds}`
        }
    },

    getDisplayTimer() {
      let currentSeconds = Math.floor(Date.now() / 1000)
      let elapsedSeconds = currentSeconds - this.startSeconds
      if (elapsedSeconds > (this.blindSchedule[this.currentStep]['minutes'] * 60)) {
        this.startSeconds = Math.floor(Date.now() / 1000)
        this.currentStep += 1
        return {"minutes": this.blindSchedule[this.currentStep]['minutes'], "seconds": "00"}
      }
      let x = ((this.blindSchedule[this.currentStep]['minutes'] * 60) - elapsedSeconds)
      let minutes = Math.floor(x / 60)
      let seconds = Math.floor(x % 60)
      if (seconds < 10) {
        return {"minutes": minutes, "seconds": `0${seconds}`}
      } else {
        return {"minutes": minutes, "seconds": seconds}
      }
    }
  }
}

</script>

<template>
  <h1>{{ countDownValue }}</h1>
  <div v-for="s in this.blindSchedule" :key="s.step">
    <div :class="{ currentstep: (s.step == this.currentStep+1) }">
      <span>{{s.step}}</span> - 
      <span>{{s.minutes}}</span> - 
      <span>{{s.smallBlind}}</span> - 
      <span>{{s.bigBlind}}</span>
    </div>
  </div>

</template>

<style scoped>
.currentstep {
  border: 1px solid red;
}
</style>
