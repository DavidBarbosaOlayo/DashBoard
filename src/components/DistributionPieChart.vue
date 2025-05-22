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
    TitleComponent,
    TooltipComponent,
    LegendComponent
  } from 'echarts/components'
  import { CanvasRenderer } from 'echarts/renderers'
  
  // Registrar componentes de ECharts
  use([
    CanvasRenderer,
    PieChart,
    TitleComponent,
    TooltipComponent,
    LegendComponent
  ])
  
  interface DataItem {
    name: string
    value: number
    itemStyle?: {
      color: string
    }
  }
  
  const props = defineProps<{
    data?: DataItem[]
    title?: string
  }>()
  
  // Valores por defecto
  const defaultData: DataItem[] = [
    { name: 'iOS', value: 40, itemStyle: { color: '#6366f1' } },
    { name: 'Android', value: 35, itemStyle: { color: '#10b981' } },
    { name: 'Web', value: 25, itemStyle: { color: '#f59e0b' } }
  ]
  
  // Configuración reactiva del gráfico
  const chartOption = ref({
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {c} ({d}%)',
      backgroundColor: 'rgba(17, 24, 39, 0.9)',
      borderColor: 'rgba(99, 102, 241, 0.5)',
      borderWidth: 1,
      padding: 10,
      textStyle: {
        color: '#fff',
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
        color: '#374151',
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
          borderColor: '#fff',
          borderWidth: 2
        },
        label: {
          show: false
        },
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
        labelLine: {
          show: false
        },
        data: props.data || defaultData
      }
    ]
  })
  
  // Actualizar el gráfico cuando cambian los props
  watch(() => [props.data, props.title], () => {
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
  }, { deep: true })
  
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
    height: 100%;
  }
  </style>