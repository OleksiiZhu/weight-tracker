<script setup>
import { computed, defineProps } from 'vue'
import WeightsHistoryRow from '@/components/WeightHistoryRow.vue'

const props = defineProps({
  weights: {
    type: Array,
    default: []
  }
})

const mappedWeights = computed(
  () => props.weights
    .sort((a, b) => new Date(b.date) - new Date(a.date))
    .map((item, index, array) => {
      const diff = array[index + 1]
        ? item.weight - array[index + 1].weight
        : 0
      return {
        ...item,
        diff
      }
    })
)

</script>

<template>
  <div class="weight-history">
    <h2 class="text-center text-2xl mt-5 mb-5 uppercase">Weight History:</h2>
    <div class="flex flex-col">
      <div class="overflow-x-auto sm:-mx-6 lg:-mx-8">
        <div class="py-2 inline-block min-w-full sm:px-6 lg:px-8">
          <div class="overflow-hidden">
            <table class="min-w-full text-center">
              <thead class="bg-white border-b">
              <tr>
                <th scope="col" class="table-head">
                  Weight
                </th>
                <th scope="col" class="table-head">
                  Date
                </th>
                <th scope="col" class="table-head">
                  Difference
                </th>
              </tr>
              </thead>
              <tbody v-for="weight in mappedWeights">
                <WeightsHistoryRow
                  :weight="weight.weight"
                  :date="weight.date"
                  :difference="weight.diff"
                />
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
