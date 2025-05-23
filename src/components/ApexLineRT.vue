<template>
  <div class="chart-container">
    <ApexChart
      type="line"
      height="100%"
      :options="chartOptions"
      :series="chartSeries"
    />
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, nextTick } from 'vue'
import ApexChart from 'vue3-apexcharts'
import type { ApexAxisChartSeries, ApexOptions } from 'apexcharts'

const props = defineProps<{
  series: ApexAxisChartSeries
  title: string
  kpiTarget: number
  color: string
}>()

const chartSeries = computed(() => props.series)

const chartOptions = computed<ApexOptions>(() => ({
  chart: {
    id: 'realtime',
    type: 'line',
    foreColor: '#374151',
    animations: {
      enabled: true,
      easing: 'linear',
      dynamicAnimation: { speed: 1000 }
    },
    toolbar: { show: false },
    zoom: { enabled: false },
    background: 'transparent',
    fontFamily: 'Inter, sans-serif'
  },
  stroke: {
    curve: 'smooth',
    width: 3,
    colors: [props.color]
  },
  markers: {
    size: 0,
    hover: { size: 5 },
    colors: [props.color]
  },
  xaxis: {
    type: 'datetime',
    labels: {
      style: { fontFamily: 'Inter, sans-serif' },
      datetimeFormatter: {
        year: 'yyyy',
        month: "MMM 'yy",
        day: 'dd MMM',
        hour: 'HH:mm:ss'
      }
    },
    axisBorder: { show: false },
    axisTicks: { show: false }
  },
  yaxis: {
    min: 0,
    max: 100,
    tickAmount: 5
  },
  tooltip: {
    x: { format: 'HH:mm:ss' },
    theme: 'dark'
  },
  grid: {
    borderColor: '#e5e7eb',
    strokeDashArray: 4,
    yaxis: { lines: { show: true } },
    padding: { top: 0, right: 0, bottom: 0, left: 10 }
  },
  title: {
    text: props.title,
    align: 'left',
    style: {
      fontSize: '16px',
      fontFamily: 'Inter, sans-serif',
      fontWeight: 600
    }
  },
  annotations: {
    yaxis: [{
      y: props.kpiTarget,
      borderColor: '#f59e0b',
      borderWidth: 2,
      strokeDashArray: 4,
      label: {
        borderColor: '#f59e0b',
        style: { color: '#000000', background: '#f59e0b' },
        text: 'Objetivo'
      }
    }]
  },
  fill: {
    type: 'solid',       // define relleno sólido
    colors: [props.color],
    opacity: 0.3         // intensidad uniforme en todo el área
  }
}))

onMounted(async () => {
  await nextTick()
  window.dispatchEvent(new Event('resize'))
})
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: calc(100% - 30px);
}
</style>
