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

/* ▸ props ------------------------------------------------------------- */
interface Props {
  value: number          // 0-100 %
  title?: string
}
const props = withDefaults(defineProps<Props>(), {
  title: 'Conversión'
})

/* ▸ opción reactiva --------------------------------------------------- */
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
              [0.2, '#ef4444'],   // 0-20% rojo
              [0.4, '#f59e0b'],   // 20-40% ámbar
              [0.6, '#6366f1'],   // 40-60% indigo
              [1.0, '#10b981']    // 60-100% verde
            ]
          }
        },
        pointer: { 
          length: '60%', 
          width: 6,
          itemStyle: {
            color: 'auto'
          }
        },
        axisTick: {
          distance: -20,
          length: 8,
          lineStyle: {
            color: '#fff',
            width: 2
          }
        },
        splitLine: {
          distance: -20,
          length: 20,
          lineStyle: {
            color: '#fff',
            width: 3
          }
        },
        axisLabel: {
          color: '#374151', // Cambiar de '#6b7280' a un color más oscuro
          distance: -40,
          fontSize: 12,
          fontFamily: 'Inter, sans-serif'
        },
        detail: {
          valueAnimation: true,
          fontSize: 28,
          fontWeight: 'bold',
          formatter: '{value}%',
          color: '#111827',
          offsetCenter: [0, '30%'],
          fontFamily: 'Inter, sans-serif'
        },
        title: {
          offsetCenter: [0, '70%'],
          color: '#374151', // Cambiar de '#6b7280' a un color más oscuro
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
  // Asegurarse de que el gráfico se renderice correctamente
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
  overflow: hidden; /* Añadir overflow hidden */
  position: relative; /* Asegurar posicionamiento correcto */
}
.gauge {
  width: 100%;
  height: 100%;
}
</style>
