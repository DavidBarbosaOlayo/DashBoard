<template>
  <div class="chart-container">
    <ApexChart
      type="donut"
      height="100%"
      :options="chartOptions"
      :series="chartSeries"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, nextTick } from 'vue'
import ApexChart from 'vue3-apexcharts'
import type { ApexOptions } from 'apexcharts'

interface Props {
  labels?: string[]
  values?: number[]
  colors?: string[]
  title?: string
}

const props = withDefaults(defineProps<Props>(), {
  labels: () => ['ES','FR','IT','DE','PT'],
  values: () => [350,200,100,80,70],
  colors: () => ['#6366f1','#10b981','#f59e0b','#ef4444','#8b5cf6'],
  title: 'Agricultores'
})

const chartSeries = ref<number[]>(props.values)

const chartOptions = ref<ApexOptions>({
  chart: {
    foreColor: '#374151',           // <- color oscuro para todo el texto
    background: 'transparent',
    fontFamily: 'Inter, sans-serif',
    animations: {
      enabled: true,
      easing: 'easeinout',
      speed: 800,
      animateGradually: { enabled: true, delay: 150 },
      dynamicAnimation: { enabled: true, speed: 350 }
    }
  },
  labels: props.labels,
  colors: props.colors,
  legend: {
    position: 'bottom',
    fontFamily: 'Inter, sans-serif',
    fontSize: '14px',
    markers: { width: 12, height: 12, radius: 6 },
    itemMargin: { horizontal: 10, vertical: 5 },
    labels: { colors: '#374151' }
  },
  plotOptions: {
    pie: {
      donut: {
        size: '65%',
        labels: {
          show: true,
          name: {
            fontSize: '16px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 600,
            color: '#111827'
          },
          value: {
            fontSize: '20px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 700,
            color: '#111827'
          },
          total: {
            label: 'Total',
            fontSize: '16px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 600,
            color: '#111827',
            formatter: w => w.globals.seriesTotals.reduce((a,b)=>a+b,0).toString()
          }
        }
      }
    }
  },
  dataLabels: { enabled: false },
  stroke: { width: 2, colors: ['#fff'] },
  tooltip: { enabled: true, theme: 'light' },
  responsive: [{
    breakpoint: 480,
    options: {
      chart: { height: 300 },
      legend: { position: 'bottom' }
    }
  }]
})

watch(props, () => {
  chartSeries.value = props.values!
  chartOptions.value = {
    ...chartOptions.value,
    labels: props.labels,
    colors: props.colors
  }
})

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
