<template>
  <div class="chart-container">
    <div ref="chartContainer" class="echarts-container"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'
import * as echarts from 'echarts/core'
import { 
  TitleComponent, 
  TooltipComponent, 
  VisualMapComponent,
  LegendComponent
} from 'echarts/components'
import { MapChart } from 'echarts/charts'
import { CanvasRenderer } from 'echarts/renderers'
import europeGeoJSON from '@/assets/europe.geo.json'

// Registrar los componentes necesarios de ECharts
echarts.use([
  TitleComponent,
  TooltipComponent,
  VisualMapComponent,
  LegendComponent,
  MapChart,
  CanvasRenderer
])

// Definir interfaces para mejorar el tipado
interface Point { 
  country: string; 
  value: number 
}

// Props con valores por defecto
const props = withDefaults(defineProps<{ 
  data?: Point[]; 
  title?: string 
}>(), {
  data: () => [],
  title: 'Descargas UE'
})

// Referencias
const chartContainer = ref<HTMLElement | null>(null)
let chartInstance: echarts.ECharts | null = null

// Preparar datos para ECharts
function prepareEChartsData() {
  // Convertir los datos al formato que espera ECharts
  return props.data.map(item => {
    return {
      name: item.country,
      value: item.value
    }
  })
}

// Inicializar el gráfico
async function initChart() {
  if (!chartContainer.value) {
    console.error('Chart container reference is null')
    return
  }
  
  try {
    // Esperar a que el DOM se actualice
    await nextTick()
    
    // Registrar el mapa de Europa
    echarts.registerMap('europe', europeGeoJSON as any)
    
    // Crear la instancia de ECharts
    chartInstance = echarts.init(chartContainer.value)
    
    // Configurar las opciones del gráfico
    const option = {
      tooltip: {
        trigger: 'item',
        formatter: '{b}: {c}',
        backgroundColor: 'rgba(0, 0, 0, 0.75)',
        borderWidth: 0,
        textStyle: {
          color: '#fff'
        }
      },
      visualMap: {
        left: 'right',
        min: 0,
        max: Math.max(...props.data.map(item => item.value)),
        text: ['Alto', 'Bajo'],
        realtime: false,
        calculable: true,
        inRange: {
          color: ['#e0f2fe', '#0ea5e9', '#0369a1']
        },
        textStyle: {
          color: '#374151'
        },
        itemWidth: 10,
        itemHeight: 80,
        padding: [0, 10, 0, 0]
      },
      series: [
        {
          name: props.title,
          type: 'map',
          map: 'europe',
          roam: true,
          emphasis: {
            label: {
              show: true,
              color: '#fff'
            },
            itemStyle: {
              areaColor: '#0284c7'
            }
          },
          select: {
            disabled: true
          },
          itemStyle: {
            areaColor: '#f1f5f9',
            borderColor: '#fff',
            borderWidth: 1
          },
          data: prepareEChartsData(),
          nameMap: {
            // Asegurarse de que los nombres coincidan con los del GeoJSON
            'Spain': 'Spain',
            'Germany': 'Germany',
            'France': 'France',
            'Italy': 'Italy',
            'Netherlands': 'Netherlands',
            'Poland': 'Poland',
            'Portugal': 'Portugal'
          }
        }
      ]
    }
    
    // Aplicar las opciones
    chartInstance.setOption(option)
    
    // Ajustar el mapa para que se vean los países seleccionados
    setTimeout(() => {
      if (chartInstance) {
        chartInstance.resize()
      }
    }, 100)
  } catch (error) {
    console.error('Error al inicializar el gráfico:', error)
  }
}

// Actualizar el gráfico cuando cambian los datos
function updateChart() {
  if (!chartInstance) return
  
  try {
    const option = {
      visualMap: {
        max: Math.max(...props.data.map(item => item.value))
      },
      series: [
        {
          data: prepareEChartsData()
        }
      ]
    }
    
    chartInstance.setOption(option)
  } catch (error) {
    console.error('Error al actualizar el gráfico:', error)
  }
}

// Ciclo de vida
onMounted(async () => {
  // Esperar a que el DOM se actualice completamente
  setTimeout(() => {
    initChart()
  }, 300)
})

// Observar cambios en los datos
watch(
  () => props.data,
  () => {
    updateChart()
  },
  { deep: true }
)

// Manejar cambios de tamaño de ventana
const handleResize = () => {
  if (chartInstance) {
    chartInstance.resize()
  }
}

window.addEventListener('resize', handleResize)

// Limpiar al desmontar
onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  if (chartInstance) {
    chartInstance.dispose()
    chartInstance = null
  }
})
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: calc(100% - 30px);
  position: relative;
}
.echarts-container {
  width: 100%;
  height: 100%;
}
</style>
