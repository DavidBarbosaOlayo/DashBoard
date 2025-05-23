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
  series:     () => [{ name: 'Recintos Activos', data: [5,12,8,15,10,18,20] }],
  categories: () => ['Ene','Feb','Mar','Abr','May','Jun','Jul'],
  title:      'Evolución',
  color:      '#10b981'
})

const chartSeries = ref<Serie[]>(props.series!)

const chartOptions = ref<ApexOptions>({
  chart: {
    foreColor:  '#374151',
    background: 'transparent',
    toolbar:    { show: false },
    fontFamily: 'Inter, sans-serif',
    dropShadow: {
      enabled: true,
      top:     2,
      left:    0,
      blur:    6,
      opacity: 0.4
    },
    animations: {
      enabled: true,
      easing:  'easeinout',
      speed:   800,
      animateGradually: { enabled: true, delay: 150 },
      dynamicAnimation: { enabled: true, speed: 350 }
    },
    offsetX: 10
  },
  colors: [props.color!],
  stroke: {
    curve: 'smooth',
    width: 5
  },
  markers: {
    size:         8,
    colors:       [props.color!],
    strokeColors: '#fff',
    strokeWidth:  2,
    hover:        { size: 10 }
  },
  xaxis: {
    categories:    props.categories,
    offsetX:       0,
    tickPlacement: 'between',
    labels: {
      style: {
        fontFamily: 'Inter, sans-serif',
        fontWeight: 500
      }
    },
    axisBorder: { show: false },
    axisTicks:  { show: false }
  },
  yaxis: {
    min:           0,
    forceNiceScale: true,
    tickAmount:     4,
    title: {
      style: {
        fontFamily: 'Inter, sans-serif',
        fontWeight: 500
      }
    },
    labels: {
      style: {
        fontFamily: 'Inter, sans-serif'
      }
    }
  },
  grid: {
    borderColor:    '#e5e7eb',
    strokeDashArray: 4,
    padding: {
      left:   30,
      right:  10,
      top:    10,
      bottom: 10
    }
  },
  fill: {
    type: 'gradient',
    gradient: {
      shade:         'light',
      type:          'vertical',
      shadeIntensity: 0.5,
      inverseColors: false,
      opacityFrom:   0.6,
      opacityTo:     0.1,
      stops:         [0, 90, 100]
    }
    // Si prefieres sin relleno, usar:
    // opacity: 0
  },
  tooltip: {
    theme: 'dark',
    x:     { show: true },
    y:     { title: { formatter: seriesName => seriesName } },
    marker: { show: true }
  }
})

watch(props, () => {
  chartSeries.value = props.series!
  chartOptions.value = {
    ...chartOptions.value,
    colors: [props.color!],
    xaxis: {
      ...(chartOptions.value.xaxis as any),
      categories: props.categories
    }
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
