<template>
  <div class="chart-container">
    <ApexChart
      type="bar"
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

interface Serie { name: string; data: number[] }
interface Props {
  series?: Serie[]
  categories?: string[]
  title?: string
  color?: string
}
const props = withDefaults(defineProps<Props>(), {
  series: () => [{ name: 'Actividades', data: [320,450,390,580,610,720,800] }],
  categories: () => ['Sem 1','Sem 2','Sem 3','Sem 4','Sem 5','Sem 6','Sem 7'],
  title: 'Uso semanal',
  color: '#6366f1'
})

const chartSeries = ref<Serie[]>(props.series)

function yFormatter(value: number) {
  return value.toFixed(0)
}

const chartOptions = ref<ApexOptions>({
  chart: {
    id: 'bar',
    foreColor: '#374151',
    background: 'transparent',
    toolbar: { show: false },
    fontFamily: 'Inter, sans-serif',
    animations: {
      enabled: true,
      easing: 'easeinout',
      speed: 800,
      animateGradually: { enabled: true, delay: 150 },
      dynamicAnimation: { enabled: true, speed: 350 }
    }
  },
  colors: [props.color],
  plotOptions: {
    bar: { borderRadius: 6, columnWidth: '60%' }
  },
  dataLabels: { enabled: false },
  xaxis: {
    categories: props.categories,
    labels: { style: { fontFamily: 'Inter, sans-serif', fontWeight: 500 } },
    axisBorder: { show: false },
    axisTicks: { show: false }
  },
  yaxis: {
    labels: { formatter: yFormatter },
  },
  tooltip: {
    theme: 'dark',                     // <- tooltip oscuro
    x: { show: true },
    y: { title: { formatter: seriesName => seriesName } }
  },
  grid: {
    borderColor: '#e5e7eb',
    strokeDashArray: 4,
    padding: { top: 0, right: 0, bottom: 0, left: 10 }
  },
  states: {
    hover: { filter: { type: 'darken', value: 0.9 } },
    active: { filter: { type: 'darken', value: 0.85 } }
  },
  fill: {
    type: 'gradient',
    gradient: {
      shade: 'light',
      type: 'vertical',
      shadeIntensity: 0.2,
      inverseColors: false,
      opacityFrom: 1,
      opacityTo: 0.85,
      stops: [0, 90, 100]
    }
  }
})

watch(props, () => {
  chartSeries.value = props.series!
  chartOptions.value = {
    ...chartOptions.value,
    colors: [props.color!],
    xaxis: { ...chartOptions.value.xaxis!, categories: props.categories }
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
