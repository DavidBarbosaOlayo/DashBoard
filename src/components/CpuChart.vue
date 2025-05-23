<template>
  <div class="chart-container">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import Chart from 'chart.js/auto'

const props = defineProps<{
  title?: string
  color?: string
  data?: number[]
  labels?: string[]
}>()

const chartCanvas = ref<HTMLCanvasElement | null>(null)
let chartInstance: Chart | null = null

// Valores por defecto
const defaultData = [28, 45, 35, 55, 40, 65, 80, 74, 68, 85, 75, 62]
const defaultLabels = Array.from({ length: 12 }, (_, i) => `${i}:00`)

// Función para crear o actualizar el gráfico
const createOrUpdateChart = () => {
  if (!chartCanvas.value) return

  const ctx = chartCanvas.value.getContext('2d')
  if (!ctx) return

  if (chartInstance) {
    chartInstance.destroy()
  }

  chartInstance = new Chart(ctx, {
    type: 'line',
    data: {
      labels: props.labels || defaultLabels,
      datasets: [
        {
          label: props.title || 'CPU Usage',
          data: props.data || defaultData,
          borderColor: props.color || '#ef4444',
          backgroundColor(context: any) {
            const chart = context.chart
            const { ctx, chartArea } = chart
            if (!chartArea) return null
            const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top)
            gradient.addColorStop(0, 'rgba(239, 68, 68, 0)')
            gradient.addColorStop(1, 'rgba(239, 68, 68, 0.3)')
            return gradient
          },
          borderWidth: 3,
          tension: 0.4,
          fill: true,
          pointBackgroundColor: props.color || '#ef4444',
          pointBorderColor: '#fff',
          pointBorderWidth: 2,
          pointRadius: 4,
          pointHoverRadius: 6
        }
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: { display: false },
        tooltip: {
          mode: 'index',
          intersect: false,
          backgroundColor: 'rgba(17, 24, 39, 0.9)',
          titleColor: '#E5E7EB',
          bodyColor: '#E5E7EB',
          borderColor: 'rgba(239, 68, 68, 0.5)',
          borderWidth: 1,
          padding: 10,
          displayColors: false,
          callbacks: {
            label(ctx) {
              return `${ctx.parsed.y}% CPU`
            }
          }
        }
      },
      scales: {
        x: {
          grid: { display: false },
          ticks: {
            color: '#E5E7EB',
            font: { family: 'Inter, sans-serif' }
          }
        },
        y: {
          beginAtZero: true,
          max: 100,
          grid: {
            color: 'rgba(229, 231, 235, 0.5)',
            drawBorder: false
          },
          ticks: {
            color: '#E5E7EB',
            font: { family: 'Inter, sans-serif' },
            callback(value) {
              return value + '%'
            }
          }
        }
      },
      interaction: {
        mode: 'nearest',
        axis: 'x',
        intersect: false
      },
      elements: {
        line: { borderJoinStyle: 'round' }
      }
    }
  })
}

onMounted(() => {
  createOrUpdateChart()
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  if (chartInstance) chartInstance.destroy()
  window.removeEventListener('resize', handleResize)
})

watch(() => [props.data, props.labels], () => {
  createOrUpdateChart()
})

const handleResize = () => {
  if (chartInstance) chartInstance.resize()
}
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
