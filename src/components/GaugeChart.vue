<template>
  <div class="gauge-container">
    <VEChart class="gauge" :option="options" autoresize />
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect, onMounted } from 'vue'
import VEChart from 'vue-echarts'
import { use } from 'echarts/core'
import { GaugeChart } from 'echarts/charts'
import { CanvasRenderer } from 'echarts/renderers'
import { TooltipComponent } from 'echarts/components'

use([GaugeChart, CanvasRenderer, TooltipComponent])

interface Props {
  value: number
  title?: string
}
const props = withDefaults(defineProps<Props>(), {
  title: 'Conversión'
})

const options = ref({})

watchEffect(() => {
  options.value = {
    series: [
      {
        type: 'gauge',
        min: 0,
        max: 100,
        radius: '90%',
        splitNumber: 5,
        axisLine: {
          lineStyle: {
            width: 20,
            color: [
              [0.2, '#ef4444'],
              [0.4, '#f59e0b'],
              [0.6, '#6366f1'],
              [1.0, '#10b981']
            ]
          }
        },
        pointer: {
          length: '60%',
          width: 6,
          itemStyle: { color: 'auto' }
        },
        axisTick: {
          distance: -20,
          length: 8,
          lineStyle: {
            color: '#E5E7EB',
            width: 2
          }
        },
        splitLine: {
          distance: -20,
          length: 20,
          lineStyle: {
            color: '#E5E7EB',
            width: 3
          }
        },
        axisLabel: {
          color: '#E5E7EB',
          distance: -40,
          fontSize: 12,
          fontFamily: 'Inter, sans-serif'
        },
        detail: {
          valueAnimation: true,
          fontSize: 28,
          fontWeight: 'bold',
          formatter: '{value}%',
          color: '#E5E7EB',
          offsetCenter: [0, '30%'],
          fontFamily: 'Inter, sans-serif'
        },
        title: {
          offsetCenter: [0, '70%'],
          color: '#E5E7EB',
          fontSize: 14,
          fontFamily: 'Inter, sans-serif'
        },
        data: [{ value: props.value, name: props.title }],
        animation: true,
        animationDuration: 1000
      }
    ]
  }
})

onMounted(() => {
  setTimeout(() => {
    window.dispatchEvent(new Event('resize'))
  }, 300)
})
</script>

<style scoped>
.gauge-container {
  width: 100%;
  height: calc(100% - 30px);
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  position: relative;
}
.gauge {
  width: 100%;
  height: 100%;
}
</style>
