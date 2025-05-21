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

echarts.use([TitleComponent, TooltipComponent, VisualMapComponent, LegendComponent, MapChart, CanvasRenderer])

interface Point { country: string; value: number }

const props = withDefaults(defineProps<{ data?: Point[]; title?: string }>(), {
  data: () => [],
  title: 'Descargas UE'
})

const chartContainer = ref<HTMLElement|null>(null)
let chartInstance: echarts.ECharts | null = null

function prepareEChartsData() {
  return props.data!.map(item => ({ name: item.country, value: item.value }))
}

function initChart() {
  if (!chartContainer.value) return
  echarts.registerMap('europe', europeGeoJSON as any)
  chartInstance = echarts.init(chartContainer.value)
  chartInstance.setOption({
    tooltip: { trigger: 'item', formatter: '{b}: {c}', backgroundColor: 'rgba(0,0,0,0.75)', textStyle: { color: '#fff' } },
    visualMap: {
      left: 'right',
      min: 0,
      max: Math.max(...props.data!.map(d => d.value)),
      text: ['Alto','Bajo'],
      calculable: true,
      inRange: { color: ['#e0f2fe','#0ea5e9','#0369a1'] },
      textStyle: { color: '#374151' }
    },
    series: [{
      name: props.title,
      type: 'map',
      map: 'europe',
      roam: true,
      emphasis: { label: { show: true, color: '#fff' }, itemStyle: { areaColor: '#0284c7' } },
      itemStyle: { areaColor: '#f1f5f9', borderColor: '#fff', borderWidth: 1 },
      data: prepareEChartsData(),
      nameMap: { Spain: 'Spain', Germany: 'Germany', France: 'France', Italy: 'Italy', Netherlands: 'Netherlands', Poland: 'Poland', Portugal: 'Portugal' }
    }]
  })
}

function handleResize() {
  if (chartInstance) chartInstance.resize()
}

onMounted(async () => {
  await nextTick()
  initChart()
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  if (chartInstance) chartInstance.dispose()
})

watch(() => props.data, () => {
  if (chartInstance) {
    chartInstance.setOption({ visualMap: { max: Math.max(...props.data!.map(d => d.value)) }, series: [{ data: prepareEChartsData() }] })
  }
}, { deep: true })
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
