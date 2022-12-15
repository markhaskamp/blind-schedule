<script>
import { ref } from 'vue'

export default {
  data() {
    return {
      msg: "eddie would go",
      blindSchedule: [
        { step: 1, minutes: 30, smallBlind: 50, bigBlind: 100 },
        { step: 2, minutes: 30, smallBlind: 100, bigBlind: 200 },
        { step: 3, minutes: 30, smallBlind: 150, bigBlind: 300 },
        { step: 4, minutes: 15, smallBlind: 200, bigBlind: 400 }
      ]
    }
  }
}

</script>

<template>
  <h1>{{ msg }}</h1>
  <div v-for="s in this.blindSchedule" :key="s.step">
    <span>{{s.step}}</span> - <span>{{s.minutes}}</span> - <span>{{s.smallBlind}}</span><span>{{s.bigBlind}}</span>
  </div>

</template>

<style scoped>
</style>
