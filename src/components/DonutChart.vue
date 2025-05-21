<template>
  <div class="chart-container">
    <ApexChart
      type="donut"
      height="100%"
      :options="chartOptions"
      :series="chartValues"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'
import ApexChart from 'vue3-apexcharts'
import type { ApexOptions } from 'apexcharts'

/* ─── props ───────────────────────────────────────── */
interface Props {
  labels?: string[]
  values?: number[]
  colors?: string[]
  title?: string
}
const props = withDefaults(defineProps<Props>(), {
  labels: () => ['ES', 'FR', 'IT', 'DE', 'PT'],
  values: () => [350, 200, 100, 80, 70],
  colors: () => ['#6366f1', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6'],
  title: 'Agricultores activos'
})

/* ─── reactive state ─────────────────────── */
const chartValues = ref<number[]>(props.values)
const chartOptions = ref<ApexOptions>({
  chart: {
    background: 'transparent',
    fontFamily: 'Inter, sans-serif',
    animations: {
      enabled: true,
      easing: 'easeinout',
      speed: 800,
      animateGradually: {
        enabled: true,
        delay: 150
      },
      dynamicAnimation: {
        enabled: true,
        speed: 350
      }
    }
  },
  colors: props.colors,
  labels: props.labels,
  legend: {
    position: 'bottom',
    fontFamily: 'Inter, sans-serif',
    fontWeight: 500,
    fontSize: '14px',
    markers: {
      width: 12,
      height: 12,
      radius: 6
    },
    itemMargin: {
      horizontal: 10,
      vertical: 5
    },
    labels: {
      colors: '#374151' // Añadir colores más oscuros para las etiquetas de la leyenda
    }
  },
  plotOptions: {
    pie: {
      donut: {
        size: '65%',
        labels: {
          show: true,
          name: {
            show: true,
            fontSize: '16px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 600,
            color: '#111827'
          },
          value: {
            show: true,
            fontSize: '20px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 700,
            color: '#111827',
            formatter: function(val) {
              return val.toString()
            }
          },
          total: {
            show: true,
            label: 'Total',
            fontSize: '16px',
            fontFamily: 'Inter, sans-serif',
            fontWeight: 600,
            color: '#6b7280',
            formatter: function(w) {
              return w.globals.seriesTotals.reduce((a: number, b: number) => a + b, 0).toString()
            }
          }
        }
      }
    }
  },
  dataLabels: {
    enabled: false
  },
  stroke: {
    width: 2,
    colors: ['#fff']
  },
  tooltip: {
    enabled: true,
    theme: 'light',
    fillSeriesColor: false
  },
  states: {
    hover: {
      filter: {
        type: 'darken',
        value: 0.9
      }
    },
    active: {
      filter: {
        type: 'darken',
        value: 0.85
      }
    }
  },
  responsive: [{
    breakpoint: 480,
    options: {
      chart: {
        height: 300
      },
      legend: {
        position: 'bottom'
      }
    }
  }]
})

/* actualiza si cambian props */
watch(props, () => {
  chartValues.value = props.values
  chartOptions.value = {
    ...chartOptions.value,
    colors: props.colors,
    labels: props.labels
  }
})

onMounted(() => {
  // Asegurarse de que el gráfico se renderice correctamente
  setTimeout(() => {
    window.dispatchEvent(new Event('resize'))
  }, 300)
})
</script>

<style scoped>
.chart-container { 
  width: 100%; 
  height: calc(100% - 30px);
}
</style>
