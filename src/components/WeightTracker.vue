<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'
import WeightHistory from "@/components/WeightHistory";

const weights = ref([]);
const weightInput = ref(0);
const datePicker = ref(new Date().toISOString().slice(0, 10));
const weightChartEl = ref(null);
const weightChart = shallowRef(null);

const sortDate = (a, b) => {
  return new Date(a.date) - new Date(b.date)
}

const mapWeightDate = (weight) => {
  return weight.date
}

const mapWeight = (weight) => {
  return weight.weight
}

const sortedWeights = computed(() => weights.value.sort(sortDate))

const currentWeight = computed(() => {
  return sortedWeights.value[sortedWeights.value.length - 1] || { weight: 0 }
})

const addWeight = () => {
  if (!weightInput.value) {
    return;
  }
  weights.value.push({
    weight: weightInput.value,
    date: datePicker.value,
  })
}

watch(sortedWeights, (newWeights) => {
  const weight = [...newWeights]
  if (weightChart.value) {
    weightChart.value.data.labels = weight
      .map(mapWeightDate)
    weightChart.value.data.datasets[0].data = weight
      .map(mapWeight)
    weightChart.value.update()
    return
  }

  nextTick(() => {
    weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: weight
          .map(mapWeightDate),
        datasets: [
          {
            label: 'WEIGHT',
            data: weight
              .map(mapWeight),
            backgroundColor: 'rgba(59, 130, 246, 0.2)',
            borderColor: 'rgba(0, 97, 255, 1)',
            borderWidth: 1,
            fill: true
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false
      }
    })
  })

}, {deep: true})
</script>

<template>
  <main class="container">
    <div class="flex justify-center items-center">
      <img src="../assets/logo.png" class="w-[35px] h-[35px] m-[0] mr-[10px]" alt="">
      <h1 class="text-center text-4xl mt-5 mb-5 uppercase">Weight Tracker</h1>
    </div>
    <div
        class="flex flex-col justify-center items-center text-center rounded-full w-[200px] h-[200px] m-[auto] mb-5 border-2 border-blue-500 bg-white shadow-lg shadow-blue-500/50">
      <span class="text-4xl font-bold">{{ currentWeight.weight }}</span>
      <small class="text-2xl font-bold">Current Weight (kg)</small>
    </div>

    <form class="flex mb-5 text-center" @submit.prevent="addWeight">
      <input
          class="mr-[10px] shadow appearance-none border rounded w-[45%] py-2 px-3 text-gray-700 transition-all hover:ring-1 hover:border-blue-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:border-blue-500"
          type="number" step="0.1" v-model="weightInput"/>
      <input
          class="mr-[10px] shadow appearance-none border rounded w-[45%] py-2 px-3 text-gray-700 transition-all hover:ring-1 hover:border-blue-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:border-blue-500"
          type="date" v-model="datePicker"/>
      <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
      >Add weight
      </button>
    </form>

    <div v-if="weights && weights.length">
      <div class="w-[100%] h-[400px] m-[auto]">
        <canvas ref="weightChartEl"></canvas>
      </div>

      <WeightHistory :weights="weights" />
    </div>
  </main>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

body {
  background-color: #ededed;
}
</style>
