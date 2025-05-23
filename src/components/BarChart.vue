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
import { ref, watch, onMounted, nextTick, computed } from 'vue'
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
  series:     () => [{ name: 'Actividades', data: [320,450,390,580,610,720,800] }],
  categories: () => ['01–07 Mar','08–14 Mar','15–21 Mar','22–28 Mar','29 Mar–04 Abr','05–11 Abr','12–18 Abr'],
  title:      'Actividades',
  color:      '#F8BBD0',   // Base rosa claro
  unit:       ' actividades'
})

const chartSeries = ref<Serie[]>(props.series)

function yFormatter(value: number) {
  return value.toFixed(0)
}

// Función para interpolar entre dos colores
function interpolateColor(color1: string, color2: string, factor: number): string {
  const hex1 = color1.replace('#', '')
  const hex2 = color2.replace('#', '')
  const r1 = parseInt(hex1.substr(0, 2), 16)
  const g1 = parseInt(hex1.substr(2, 2), 16)
  const b1 = parseInt(hex1.substr(4, 2), 16)
  const r2 = parseInt(hex2.substr(0, 2), 16)
  const g2 = parseInt(hex2.substr(2, 2), 16)
  const b2 = parseInt(hex2.substr(4, 2), 16)
  const r = Math.round(r1 + (r2 - r1) * factor)
  const g = Math.round(g1 + (g2 - g1) * factor)
  const b = Math.round(b1 + (b2 - b1) * factor)
  return `#${r.toString(16).padStart(2, '0')}${g.toString(16).padStart(2, '0')}${b.toString(16).padStart(2, '0')}`
}

// Computed de colores dinámicos usando una gama de rosados/magentas
const dynamicColors = computed(() => {
  if (!props.series || props.series.length === 0) return ['#F8BBD0']

  const allValues = props.series.flatMap(serie => serie.data)
  const minValue = Math.min(...allValues)
  const maxValue = Math.max(...allValues)
  const range = maxValue - minValue

  // Gama de rosa claro a magenta intenso
  const lowColor  = '#F8BBD0' // rosa claro
  const highColor = '#C2185B' // magenta oscuro

  return allValues.map(value => {
    if (range === 0) return lowColor
    const normalized = (value - minValue) / range
    return interpolateColor(lowColor, highColor, normalized)
  })
})

const chartOptions = ref<ApexOptions>({
  chart: {
    id: 'bar',
    foreColor: '#e2e8f0',
    background: 'transparent',
    toolbar: { show: false },
    fontFamily: 'Inter, sans-serif',
    animations: {
      enabled: true,
      easing: 'easeinout',
      speed: 800,
      animateGradually: { enabled: true, delay: 150 },
      dynamicAnimation: { enabled: true, speed: 350 }
    },
    dropShadow: {
      enabled: true,
      top: 3,
      left: 2,
      blur: 4,
      opacity: 0.15,
      color: '#000'
    }
  },
  colors: dynamicColors.value,
  plotOptions: {
    bar: {
      borderRadius: 8,
      columnWidth: '65%',
      distributed: true,
      dataLabels: { position: 'top' }
    }
  },
  dataLabels: {
    enabled: true,
    formatter: (val: number) => val.toFixed(0),
    style: {
      fontSize: '12px',
      fontWeight: 'bold',
      colors: ['#ffffff']
    },
    offsetY: -20,
    dropShadow: {
      enabled: true,
      top: 1,
      left: 1,
      blur: 2,
      color: 'rgba(0,0,0,0.5)',
      opacity: 0.5
    }
  },
  xaxis: {
    categories: props.categories,
    labels: {
      style: {
        fontFamily: 'Inter, sans-serif',
        fontWeight: 500,
        colors: '#cbd5e1'
      },
      rotate: -45,
      hideOverlappingLabels: true,
      trim: true,
      maxHeight: 120
    },
    axisBorder: { show: false },
    axisTicks: { show: false },
    crosshairs: {
      show: true,
      position: 'back',
      stroke: { color: '#475569', width: 1, dashArray: 3 }
    }
  },
  yaxis: {
    title: {
      text: props.yAxisTitle,
      style: {
        fontFamily: 'Inter, sans-serif',
        fontWeight: 500,
        color: '#e2e8f0'
      }
    },
    labels: {
      formatter: yFormatter,
      style: { colors: '#cbd5e1' }
    },
    min: 0,
    forceNiceScale: true,
    tickAmount: 5
  },
  tooltip: {
    theme: 'dark',
    x: { show: true },
    y: {
      title: { formatter: (seriesName) => seriesName },
      formatter: (value) => `${value.toLocaleString()}${props.unit}`
    },
    marker: { show: false },
    style: { fontSize: '12px', fontFamily: 'Inter, sans-serif' }
  },
  grid: {
    borderColor: '#334155',
    strokeDashArray: 4,
    padding: { top: 20, right: 10, bottom: 10, left: 20 }
  },
  states: {
    hover: { filter: { type: 'darken', value: 0.85 } },
    active: { filter: { type: 'darken', value: 0.8 } }
  },
  fill: {
    type: 'gradient',
    gradient: {
      shade: 'dark',
      type: 'vertical',
      shadeIntensity: 0.3,
      inverseColors: false,
      opacityFrom: 1,
      opacityTo: 0.85,
      stops: [0, 90, 100]
    }
  },
  legend: { show: false },
  responsive: [
    {
      breakpoint: 768,
      options: {
        plotOptions: { bar: { columnWidth: '80%' } },
        xaxis: { labels: { rotate: -90, offsetY: 0 } }
      }
    }
  ]
})

watch(
  () => [props.series, props.categories, props.color, props.yAxisTitle],
  () => {
    chartSeries.value = props.series
    chartOptions.value = {
      ...chartOptions.value,
      colors: dynamicColors.value,
      plotOptions: {
        ...chartOptions.value.plotOptions,
        bar: {
          ...chartOptions.value.plotOptions?.bar,
          distributed: props.series.length === 1
        }
      },
      xaxis: { ...chartOptions.value.xaxis, categories: props.categories },
      yaxis: {
        ...chartOptions.value.yaxis,
        title: { ...chartOptions.value.yaxis?.title, text: props.yAxisTitle },
        min: 0
      },
      legend: { ...chartOptions.value.legend, show: props.series.length > 1 }
    }
  },
  { deep: true }
)

onMounted(async () => {
  await nextTick()
  window.dispatchEvent(new Event('resize'))
})
</script>



<style scoped>
.chart-container {
  width: 100%;
  height: calc(100% - 30px);
  padding: 5px;
}

:deep(.apexcharts-tooltip) {
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
  border-radius: 8px;
}

:deep(.apexcharts-tooltip-title) {
  background-color: #1e293b !important;
  border-bottom: 1px solid #334155 !important;
  padding: 8px 10px !important;
}

:deep(.apexcharts-tooltip-series-group) {
  padding: 8px 10px !important;
}

:deep(.apexcharts-bar-area) {
  stroke-width: 0;
  transition: all 0.3s ease;
}

:deep(.apexcharts-bar-area:hover) {
  filter: brightness(1.1);
}

:deep(.apexcharts-gridline) {
  stroke: rgba(203, 213, 225, 0.1);
}

:deep(.apexcharts-yaxis-label),
:deep(.apexcharts-xaxis-label) {
  fill: #cbd5e1;
}

:deep(.apexcharts-text) {
  fill: #e2e8f0;
}
</style>