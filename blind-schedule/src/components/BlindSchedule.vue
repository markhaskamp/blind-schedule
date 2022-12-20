<script>
import { ref } from 'vue'

let foo

export default {
  mounted() {
    // console.log('mounted(). ingress')
    this.startSeconds = Math.floor(Date.now() / 1000)

    foo = setInterval(this.someFunc, 250)
  },
  data() {
    return {
      startSeconds: 0,
      currentStep: 0,
      countDownValue: "",
      isRunning: true,
      startTime: "",
      blindSchedule: [
        { step: 1, minutes: 30, smallBlind: 50, bigBlind: 100 },
        { step: 2, minutes: 30, smallBlind: 100, bigBlind: 200 },
        { step: 3, minutes: 30, smallBlind: 150, bigBlind: 300 },
        { step: 4, minutes: 15, smallBlind: 200, bigBlind: 400 }
      ]
    }
  },
  methods: {
    someFunc() {
        if (this.isRunning) {
          let currentSeconds = Math.floor(Date.now() / 1000)
          let elapsedSeconds = currentSeconds - this.startSeconds
          let x = ((this.blindSchedule[this.currentStep]['minutes'] * 60) - elapsedSeconds)
          let minutes = Math.floor(x / 60)
          let seconds = Math.floor(x % 60)
          if (seconds < 10) {
            this.countDownValue = `${minutes}:0${seconds}`
          } else {
            this.countDownValue = `${minutes}:${seconds}`
          }
        }
    }
  }
}

</script>

<template>
  <h1>{{ countDownValue }}</h1>
  <div v-for="s in this.blindSchedule" :key="s.step">
    <span>{{s.step}}</span> - <span>{{s.minutes}}</span> - <span>{{s.smallBlind}}</span><span>{{s.bigBlind}}</span>
  </div>

</template>

<style scoped>
</style>
