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
      bankedElapsedSeconds: [],
      currentStep: 0,
      countDownValue: "",
      isRunning: true,

      isAcknowledged: true,
      isWarning1: false,
      isWarning2: false,
      isWarning3: false,
      isWarning4: false,

      blindSchedule: [
        { step: 1, minutes: 30, smallBlind: 50, bigBlind: 100},
        { step: 2, minutes: 30, smallBlind: 100, bigBlind: 200},
        { step: 3, minutes: 30, smallBlind: 150, bigBlind: 300},
        { step: 4, minutes: 15, smallBlind: 200, bigBlind: 400},
        { step: 5, minutes: 15, smallBlind: 250, bigBlind: 500},
        { step: 6, minutes: 15, smallBlind: 300, bigBlind: 600},
        { step: 7, minutes: 15, smallBlind: 400, bigBlind: 800},
        { step: 8, minutes: 15, smallBlind: 500, bigBlind: 1000}
      ]
    }
  },

  methods: {
    handleIntervalTick() {
        if (this.isRunning) {
          let display = this.getDisplayTimer()
          this.countDownValue = `${display.minutes}:${display.seconds}`
        }

        if (this.isWarning1) {
          this.isWarning1 = false
          this.isWarning2 = true
        } else if (this.isWarning2) {
          this.isWarning2 = false
          this.isWarning3 = true
        } else if (this.isWarning3) {
          this.isWarning3 = false
          this.isWarning4 = true
        } else if (this.isWarning4) {
          this.isWarning4 = false
          this.isWarning1 = true
        }
        
    },

    getDisplayTimer() {
      let currentSeconds = Math.floor(Date.now() / 1000)
      let elapsedSeconds = currentSeconds - this.startSeconds
      let bankedSeconds = this.bankedElapsedSeconds.reduce((acc, cur) => acc + cur, 0)
      elapsedSeconds += bankedSeconds

      if (this.isNextStep(elapsedSeconds)) {
        this.startNextStep()
      } else {
        let x = ((this.blindSchedule[this.currentStep]['minutes'] * 60) - elapsedSeconds)
        let minutes = Math.floor(x / 60)
        let seconds = Math.floor(x % 60)
        if (seconds < 10) {
          return {"minutes": minutes, "seconds": `0${seconds}`}
        } else {
          return {"minutes": minutes, "seconds": seconds}
        }
      }
    },

    isNextStep(elapsedSeconds) {
      return (elapsedSeconds > (this.blindSchedule[this.currentStep]['minutes'] * 60))
    },

    startNextStep() {
      this.startSeconds = Math.floor(Date.now() / 1000)
      this.bankedElapsedSeconds = []
      this.currentStep += 1
      let minutes = this.blindSchedule[this.currentStep]['minutes']
      if (minutes < 1) {
        minutes = 0
      }
      this.isAcknowledged = false
      this.isWarning1 = true
      return {"minutes": minutes , "seconds": "00"}
    },

    handlePauseClick() {
      if (this.isRunning) {
        let currentSeconds = Math.floor(Date.now() / 1000)
        let elapsedSeconds = currentSeconds - this.startSeconds
        this.bankedElapsedSeconds.push(elapsedSeconds)
      } else {
        this.startSeconds = Math.floor(Date.now() / 1000)
      }
      this.isRunning = !this.isRunning
    },

    handleAckClick() {
      this.isAcknowledged = true
      this.isWarning1 = false
      this.isWarning2 = false
      this.isWarning3 = false
      this.isWarning4 = false
    }

  }
}

</script>

<template>
  <div>
    <div class="timerLeft">&nbsp;</div>
    <div class="timer">
      <span :class="{ warningNone: isAcknowledged, warning1: isWarning1, warning2: isWarning2, warning3: isWarning3, warning4: isWarning4 }">{{ countDownValue }}</span>
    </div>
    <div class="timerRight">
      <button v-if="!isAcknowledged" @click="handleAckClick">Ack</button>
      <span v-else>&nbsp;</span>
    </div>
  </div>
  <div class="timerControls" style="clear:left">
    <button id="pauseButton" @click="handlePauseClick">{{ this.isRunning ? 'Pause' : 'Resume' }}</button>
  </div>
  <div class="schedule" style="textalign: center">
    <span class="duration">&nbsp;</span>
    <span class="small">small</span>
    <span class="big">big</span>
    <span class="big">&nbsp;</span>
  </div>
  <div v-for="s in this.blindSchedule" :key="s.step">
    <div :class="['schedule', { currentstep: (s.step == this.currentStep+1) }]">
      <span>{{s.minutes}}</span>
      <span>{{s.smallBlind}}</span>
      <span>{{s.bigBlind}}</span>
      <span><button class="resetButton">reset</button></span>
    </div>
  </div>

</template>

<style scoped>
.timerLeft {
  width: 14%;
  float: left;
}
.timer {
  width: 70%;
  float: left;
  color: lightgreen;
  background-color: #696969;
  font-family: monaco;
  font-size: 4.0em;
}
.warningNone {
  color: lightgreen;
  background-color: #696969;
}
.warning1 {
  color: white;
  background-color: red;
}
.warning2 {
  color: white;
  background-color: red;
}
.warning3 {
  color: red;
  background-color: yellow;
}
.warning4 {
  color: red;
  background-color: yellow;
}
.timerRight {
  float: left;
  text-align: right;
  width: 14%;
}
.currentstep {
  color: #006600;
  background-color: white;
  font-weight: bold;
}
.timerControls {
  padding-top: 0.5em;
  padding-bottom: 1.5em;
}
#pauseButton {
  width: 100px;
  height: 2.25em;
}
.schedule span {
  display: inline-block;
  width: 125px;
  font-size: 2.25em;
}
.resetButton {
  vertical-align: middle;
  height: 2.25em;
}
</style>
