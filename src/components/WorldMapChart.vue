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

echarts.use([
  TitleComponent,
  TooltipComponent,
  VisualMapComponent,
  LegendComponent,
  MapChart,
  CanvasRenderer
])

interface Point { country: string; value: number }

const props = withDefaults(
  defineProps<{ data?: Point[]; title?: string }>(),
  {
    data: () => [
      { country: 'Spain', value: Math.floor(Math.random() * 12001) },
      { country: 'Germany', value: Math.floor(Math.random() * 12001) },
      { country: 'France', value: Math.floor(Math.random() * 12001) },
      { country: 'Italy', value: Math.floor(Math.random() * 12001) },
      { country: 'Netherlands', value: Math.floor(Math.random() * 12001) },
      { country: 'Poland', value: Math.floor(Math.random() * 12001) },
      { country: 'Portugal', value: Math.floor(Math.random() * 12001) }
    ],
    title: 'Descargas UE'
  }
)

const chartContainer = ref<HTMLElement|null>(null)
let chartInstance: echarts.ECharts | null = null

function prepareData() {
  return props.data!.map(d => ({ name: d.country, value: d.value }))
}

function initChart() {
  if (!chartContainer.value) return
  echarts.registerMap('europe', europeGeoJSON as any)
  chartInstance = echarts.init(chartContainer.value)
  
  // Calcular el valor máximo para la escala de colores
  const maxValue = Math.max(...props.data!.map(d => d.value))
  
  chartInstance.setOption({
    backgroundColor: '#1e293b', // Fondo oscuro para el gráfico
    title: {
      text: props.title,
      left: 'center',
      top: 10,
      textStyle: { 
        color: '#e2e8f0', 
        fontSize: 16,
        fontWeight: 'bold'
      }
    },
    legend: {
      orient: 'vertical',
      left: '5%',
      top: 'middle',
      data: ['Mayor concentración'],
      textStyle: { 
        color: '#ef4444', 
        fontSize: 14,
        fontWeight: 'bold'
      },
      itemStyle: {
        color: '#ef4444'
      }
    },
    tooltip: {
      trigger: 'item',
      formatter: function(params: any) {
        return `<div style="font-weight:bold;margin-bottom:5px;">${params.name}</div>
                <div>Valor: <span style="color:#38bdf8;font-weight:bold">${params.value.toLocaleString()}</span></div>`;
      },
      backgroundColor: 'rgba(15,23,42,0.9)',
      borderRadius: 8,
      padding: 10,
      textStyle: { color: '#e2e8f0' },
      extraCssText: 'box-shadow: 0 4px 8px rgba(0,0,0,0.4);'
    },
   visualMap: {
  left: 'right',
  right: '5%',
  min: 0,
  max: maxValue,
  text: ['Alto', 'Bajo'],
  calculable: true,
  // Array invertido: primero el más claro (para valores bajos), y al final el más oscuro (para valores altos)
  inRange: { 
    color: [
      '#93c5fd', // muy claro (valor bajo)
      '#60a5fa',
      '#3b82f6',
      '#1e40af',
      '#1e3a8a'  // muy oscuro (valor alto)
    ]
  },
  textStyle: { color: '#e2e8f0' }
},

    series: [
      {
        name: props.title,
        type: 'map',
        map: 'europe',
        roam: true,
        // Configuración de zoom inicial
        zoom: 2.5,
        center: [15, 50],
        aspectScale: 0.75,
        animation: true,
        animationDuration: 1000,
        animationEasing: 'cubicOut',
        emphasis: {
          label: { 
            show: true, 
            color: '#fff',
            fontSize: 12,
            fontWeight: 'bold'
          },
          itemStyle: { 
            areaColor: '#0ea5e9',
            shadowBlur: 10,
            shadowColor: 'rgba(0,0,0,0.5)'
          }
        },
        itemStyle: {
          areaColor: '#334155', // Color base de los países (gris oscuro)
          borderColor: '#1e293b', // Color de los bordes entre países
          borderWidth: 1,
          shadowColor: 'rgba(0,0,0,0.3)',
          shadowBlur: 5
        },
        data: prepareData(),
        nameMap: {
          Spain: 'España',
          Germany: 'Alemania',
          France: 'Francia',
          Italy: 'Italia',
          Netherlands: 'Países Bajos',
          Poland: 'Polonia',
          Portugal: 'Portugal'
        }
      }
    ]
  })
}

function resize() {
  chartInstance?.resize()
}

onMounted(async () => {
  await nextTick()
  initChart()
  window.addEventListener('resize', resize)
})

onUnmounted(() => {
  window.removeEventListener('resize', resize)
  chartInstance?.dispose()
})

watch(
  () => props.data,
  () => {
    const maxValue = Math.max(...props.data!.map(d => d.value))
    chartInstance?.setOption({
      visualMap: {
        max: maxValue
      },
      series: [{ data: prepareData() }]
    })
  },
  { deep: true }
)
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: calc(100% - 30px);
  background-color: #1e293b;
}

.echarts-container {
  width: 100%;
  height: 100%;
}
</style>