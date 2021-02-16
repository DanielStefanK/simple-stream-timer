<template>
  <Card>
    <h1>Konfigurieren</h1>
      <template v-for="(i, idx) in intervals" :key="idx">
        <div class="configContent">
            <input type="number" v-model.number="intervals[idx]">
            <button v-if="idx != 0" @click="del(idx)">x</button>
        </div>
      </template>

      <button @click="add">add interval</button>
      <div class="configContent">
        <input placeholder="Wiederholungen" type="number" v-model.number="reps">
      </div>

      <div class="configContent">
        <label for="autoChange">Auto Change</label>
        <input id="autoChange" type="checkbox" v-model="autoChange">
      </div>

      <div class="configContent">
        <button type="submit" @click.prevent="submitConfig">
          Submit
        </button>
      </div>
  </Card>
</template>

<script>
import { defineComponent, ref } from "vue";
import { useRouter } from 'vue-router'

import Card from "../components/Card.vue"

const setupIntervals = ()=>{
  const intervals = ref([30,5])

  const add = () => {
    intervals.value.push(1)
  }

  const del = (idx) => {
    intervals.value.splice(idx, 1)
  }

  return {
    intervals,
    add,
    del
  }
}

export default defineComponent ({

  components: {
    Card
  },

  setup ()  {

    const router = useRouter()

    const {
      intervals,
      add,
      del
    } = setupIntervals()

    const autoChange = ref(false)

    const reps = ref(10)

    const submitConfig = () => {
      if (!intervals.value.every(Number.isInteger)) {
        console.error("invalid interval")
        return
      }

      router.push({name: "Timer", query: {
        intervals: JSON.stringify(intervals.value),
        reps: reps.value,
        autoChange: JSON.stringify(autoChange.value)
      }})
    }

    return {
      intervals,
      add,
      reps,
      del,
      autoChange,
      submitConfig
    }
  }
})
</script>

<style scoped>
.configContent {
  display: flex;
}
</style>