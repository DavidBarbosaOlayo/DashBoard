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
  series: Serie[]
  categories: string[]
  title: string
  color: string
  /** Texto del eje Y, p.ej. "Usuarios" */
  yAxisTitle: string
  /** Unidad para formatear etiquetas, p.ej. " usuarios" */
  unit?: string
}

const props = withDefaults(defineProps<Props>(), {
  series:    () => [{ name: 'Actividades', data: [320,450,390,580,610,720,800] }],
  categories:() => ['01–07 Mar','08–14 Mar','15–21 Mar','22–28 Mar','29 Mar–04 Abr','05–11 Abr','12–18 Abr'],
  title:     'Actividades',
  color:     '#6366f1',
  unit:      ' actividades'
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
  dataLabels: {
    enabled: true,
    formatter: (val: number) => val.toFixed(0),
       style: {
     fontSize: '12px',
    colors: ['#ffffff']    // texto en blanco
   },
   dropShadow: {            // opcional: sombra para mejorar contraste
     enabled: true,
     top: 1,
     left: 1,
     blur: 1,
     color: 'rgba(0,0,0,0.3)'
   }
  },
  xaxis: {
    categories: props.categories,
    labels: { style: { fontFamily: 'Inter, sans-serif', fontWeight: 500 } },
    axisBorder: { show: false },
    axisTicks: { show: false }
  },
  yaxis: {
    title: { text: props.yAxisTitle, style: { fontFamily: 'Inter, sans-serif', fontWeight: 500 } },
    labels: { formatter: yFormatter }
  },
  tooltip: {
    theme: 'dark',
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
  chartSeries.value = props.series
  chartOptions.value = {
    ...chartOptions.value,
    colors: [props.color],
    xaxis:   { ...chartOptions.value.xaxis!, categories: props.categories },
    yaxis:   { ...chartOptions.value.yaxis!, title: { text: props.yAxisTitle } }
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
