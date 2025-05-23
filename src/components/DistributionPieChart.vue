<template>
  <div class="chart-container">
    <v-chart :option="chartOption" autoresize />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'
import VChart from 'vue-echarts'
import { use } from 'echarts/core'
import { PieChart } from 'echarts/charts'
import {
  TooltipComponent,
  LegendComponent
} from 'echarts/components'
import { CanvasRenderer } from 'echarts/renderers'

// Registrar solo lo necesario
use([
  CanvasRenderer,
  PieChart,
  TooltipComponent,
  LegendComponent
])

interface DataItem {
  name: string
  value: number
  itemStyle?: { color: string }
}

const props = defineProps<{
  data?: DataItem[]
  title?: string
}>()

// Valores por defecto
const defaultData: DataItem[] = [
  { name: 'iOS',     value: 40, itemStyle: { color: '#6366f1' } },
  { name: 'Android', value: 35, itemStyle: { color: '#10b981' } },
  { name: 'Web',     value: 25, itemStyle: { color: '#f59e0b' } }
]

const chartOption = ref({
  tooltip: {
    trigger: 'item',
    triggerOn: 'mousemove',
    showContent: true,
    confine: true,
    formatter: '{b}: {c} ({d}%)',
    backgroundColor: 'rgba(17, 24, 39, 0.9)',
    borderColor: 'rgba(229, 231, 235, 0.5)',
    borderWidth: 1,
    padding: 10,
    textStyle: {
      color: '#E5E7EB',
      fontFamily: 'Inter, sans-serif'
    }
  },
  legend: {
    orient: 'vertical',
    right: '5%',
    top: 'center',
    itemWidth: 10,
    itemHeight: 10,
    icon: 'circle',
    textStyle: {
      color: '#E5E7EB',
      fontFamily: 'Inter, sans-serif'
    }
  },
  series: [
    {
      name: props.title || 'Distribución',
      type: 'pie',
      radius: ['40%', '70%'],
      center: ['40%', '50%'],
      avoidLabelOverlap: true,
      itemStyle: {
        borderRadius: 6,
        borderColor: '#ffffff',
        borderWidth: 2
      },
      label: { show: false },
      emphasis: {
        label: {
          show: true,
          fontWeight: 'bold',
          formatter: '{b}: {d}%'
        },
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.2)'
        }
      },
      labelLine: { show: false },
      data: props.data || defaultData
    }
  ]
})

watch(
  () => [props.data, props.title],
  () => {
    chartOption.value = {
      ...chartOption.value,
      series: [
        {
          ...chartOption.value.series[0],
          name: props.title || 'Distribución',
          data: props.data || defaultData
        }
      ]
    }
  },
  { deep: true }
)

onMounted(() => {
  // fuerza un resize para corregir doble tooltip
  setTimeout(() => window.dispatchEvent(new Event('resize')), 300)
})
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
}
</style>
