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
  color:     '#f59e0b', // Cambiado a color ámbar
  unit:      ' actividades'
})

const chartSeries = ref<Serie[]>(props.series)

function yFormatter(value: number) {
  return value.toFixed(0)
}

// Colores para las barras - Paleta cálida sin azules
const barColors = ['#f59e0b', '#f97316', '#ef4444', '#ec4899', '#d946ef']

const chartOptions = ref<ApexOptions>({
  chart: {
    id: 'bar',
    foreColor: '#e2e8f0', // Color de texto más claro para tema oscuro
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
  colors: barColors, // Usar la paleta de colores cálidos
  plotOptions: {
    bar: { 
      borderRadius: 8, 
      columnWidth: '65%',
      distributed: true, // Distribuir colores entre barras
      dataLabels: {
        position: 'top'
      }
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
        colors: '#cbd5e1' // Color más claro para las etiquetas
      },
      rotate: -45,
      rotateAlways: false,
      hideOverlappingLabels: true,
      trim: true,
      maxHeight: 120
    },
    axisBorder: { show: false },
    axisTicks: { show: false },
    crosshairs: {
      show: true,
      position: 'back',
      stroke: {
        color: '#475569',
        width: 1,
        dashArray: 3
      }
    }
  },
  yaxis: {
    title: { 
      text: props.yAxisTitle, 
      style: { 
        fontFamily: 'Inter, sans-serif', 
        fontWeight: 500,
        color: '#e2e8f0' // Color más claro para el título
      } 
    },
    labels: { 
      formatter: yFormatter,
      style: {
        colors: '#cbd5e1' // Color más claro para las etiquetas
      }
    },
    min: 0, // Forzar que el eje Y comience en 0
    forceNiceScale: true,
    tickAmount: 5
  },
  tooltip: {
    theme: 'dark',
    x: { show: true },
    y: { 
      title: { formatter: seriesName => seriesName },
      formatter: (value) => `${value.toLocaleString()}${props.unit}`
    },
    marker: { show: false },
    style: {
      fontSize: '12px',
      fontFamily: 'Inter, sans-serif'
    }
  },
  grid: {
    borderColor: '#334155', // Color más oscuro para los bordes
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
  legend: {
    show: false // Ocultar leyenda para gráfico distribuido
  },
  responsive: [
    {
      breakpoint: 768,
      options: {
        plotOptions: {
          bar: {
            columnWidth: '80%'
          }
        },
        xaxis: {
          labels: {
            rotate: -90,
            offsetY: 0
          }
        }
      }
    }
  ]
})

watch(
  () => [props.series, props.categories, props.color, props.yAxisTitle],
  () => {
    chartSeries.value = props.series
    
    // Si el usuario proporciona un color específico que no es el predeterminado,
    // usamos ese color para todas las barras
    const newColors = props.color !== '#6366f1' ? 
      (props.series.length > 1 ? [props.color] : barColors) : 
      barColors;
    
    chartOptions.value = {
      ...chartOptions.value,
      colors: newColors,
      plotOptions: {
        ...chartOptions.value.plotOptions,
        bar: {
          ...chartOptions.value.plotOptions?.bar,
          distributed: props.series.length === 1
        }
      },
      xaxis: { 
        ...chartOptions.value.xaxis, 
        categories: props.categories 
      },
      yaxis: { 
        ...chartOptions.value.yaxis, 
        title: { 
          ...chartOptions.value.yaxis?.title,
          text: props.yAxisTitle 
        },
        min: 0 // Asegurar que siempre comience en 0
      },
      legend: {
        ...chartOptions.value.legend,
        show: props.series.length > 1
      }
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