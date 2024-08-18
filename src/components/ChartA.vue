<script setup lang="ts">
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'
import { Bar } from 'vue-chartjs'

import { ref, defineProps, watch } from 'vue'

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend)

const props = defineProps<{ temperatures: any; time: any }>()

const data = ref({
  labels: props.time,
  datasets: [
    {
      label: 'Temperatures in \u00B0C',
      backgroundColor: '#fcb00399',
      data: props.temperatures
    }
  ]
})

const options = {
  responsive: true,
  maintainAspectRatio: true
}

watch(
  () => props,
  (newProps) => {
    data.value.labels = newProps.time
    data.value.datasets[0].data = newProps.temperatures
  }
)
</script>

<template>
  <Bar :data="data" :options="options" />
</template>
