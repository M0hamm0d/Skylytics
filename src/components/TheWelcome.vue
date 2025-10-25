<template>
  <Line :data="chartData" :options="chartOptions" />
</template>

<script setup>
import { ref, onMounted, defineProps, watch } from 'vue'
import { Line } from 'vue-chartjs'

const props = defineProps({
  City: String,
  formattedYear: String,
})
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement,
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, LineElement, CategoryScale, LinearScale, PointElement)

const chartData = ref({
  labels: [],
  datasets: [],
})

const apiKey = 'HT8TLHZAA3JP82N3XPUD6PWQW'
//Note

const chartOptions = {
  responsive: true,
  color: '#fff',
  plugins: {
    legend: { position: 'top', color: 'white' },
    title: { display: true, text: 'API Data Chart', color: 'white' },
  },
  scales: {
    x: {
      ticks: {
        color: '#fff',
      },
    },
    y: {
      ticks: {
        color: '#fff',
      },
    },
  },
}
async function updateChart() {
  try {
    const response = await fetch(
      `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${props.City}/${props.formattedYear}?key=${apiKey}`,
    )
    const json = await response.json()
    chartData.value = {
      labels: json.days[0].hours.map((obj) => obj.datetime),
      datasets: [
        {
          label: 'My Dataset',
          data: json.days[0].hours.map((obj) => ((obj.temp - 32) * 5) / 9),
          fill: false,
          borderColor: '#fff',
          tension: 0.1,
        },
      ],
    }
  } catch (err) {
    console.error('Error fetching chart data:', err)
  }
}
watch(
  () => props.City,
  () => {
    updateChart()
  },
)
watch(
  () => props.formattedYear,
  () => {
    updateChart()
  },
)
onMounted(updateChart)
</script>
