<template>
    <v-chart :option="option" autoresize />
  </template>
  
  <script setup lang="ts">
  import { ref, watch } from 'vue'
  import VChart from 'vue-echarts'
  import { use } from 'echarts/core'
  import { RadarChart as EchartsRadar } from 'echarts/charts'
  import {
    TitleComponent,
    TooltipComponent,
    LegendComponent
  } from 'echarts/components'
  import { CanvasRenderer } from 'echarts/renderers'
  
  use([
    CanvasRenderer,
    EchartsRadar,
    TitleComponent,
    TooltipComponent,
    LegendComponent
  ])
  
  interface Indicator { name: string; max: number }
  
  const props = defineProps<{
    indicators: Indicator[]
    data: Array<{ value: number[]; name: string }>
  }>()
  
  const option = ref({
    tooltip: {},
    legend: {
      data: props.data.map(d => d.name),
      textStyle: {
           color: '#fff' }
    },
    radar: {
      shape: 'circle',
      splitNumber: 5,
      radius: '65%',
      indicator: props.indicators
    },
    series: [
      {
        name: 'KPIs',
        type: 'radar',
        data: props.data,
        areaStyle: { opacity: 0.3 }
      }
    ]
  })
  
  // reactivo: si cambian props, actualizar opción
  watch(
    () => [props.indicators, props.data],
    () => {
      option.value.radar.indicator = props.indicators
      option.value.series[0].data = props.data
      option.value.legend.data = props.data.map(d => d.name)
    }
  )
  </script>
  