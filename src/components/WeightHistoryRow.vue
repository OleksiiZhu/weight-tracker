<script setup>
import { computed } from 'vue'

const props = defineProps({
  weight: {
    type: String,
    default: '',
  },
  date: {
    type: String,
    default: ''
  },
  difference: {
    type: Number,
    default: 0
  }
})

const diffColor = computed(
  () => props.difference === 0
    ? 'inherit'
    : props.difference > 0
      ? '#07f107'
      : '#f32828'
)

const diffString = computed(
  () => props.difference > 0
    ? `+${props.difference}`
    : props.difference
)

</script>

<template>
  <tr class="bg-white border-b transition duration-300 ease-in-out hover:bg-blue-200">
    <td class="table-body">
      {{ weight }} kg
    </td>
    <td class="table-body">
      {{ date }}
    </td>
    <td class="table-body" :style="{ color: diffColor }">
      {{ diffString }}
    </td>
  </tr>
</template>
