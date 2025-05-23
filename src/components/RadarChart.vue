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
    orient: 'vertical',
    left: '3%',
    top: '10%',
    data: props.data.map(d => d.name),
    textStyle: { color: '#E5E7EB' },
    itemGap: 20
  },

  radar: {
    shape: 'circle',
    splitNumber: 5,
    radius: '83%',
    indicator: props.indicators,
    splitLine: { lineStyle: { color: 'rgba(229,231,235,0.2)' } },
    axisLine:  { lineStyle: { color: 'rgba(229,231,235,0.3)' } },
    splitArea: { show: false },
    name: {
      textStyle: {
        color: '#E5E7EB',
        fontFamily: 'Inter, sans-serif'
      }
    }
  },
  series: [
    {
      name: props.data[0]?.name || 'Actual',
      type: 'radar',
      data: [props.data[0]],
      lineStyle:  { color: '#F97316', width: 2 },             // naranja intenso
      itemStyle:  { color: '#F97316' },                        // naranja intenso
      areaStyle:  { color: 'rgba(249,115,22,0.6)' },           // naranja con opacidad
      label: {
        show: true,
        color: '#E5E7EB',
        fontFamily: 'Inter, sans-serif'
      }
    },
    {
      name: props.data[1]?.name || 'Objetivo',
      type: 'radar',
      data: [props.data[1]],
      areaStyle: { opacity: 0.3 },
      label: {
        show: true,
        color: '#E5E7EB',
        fontFamily: 'Inter, sans-serif'
      }
    }
  ]
})

watch(
  () => [props.indicators, props.data],
  () => {
    option.value.legend.data = props.data.map(d => d.name)
    option.value.radar.indicator = props.indicators
    option.value.series[0].data = [props.data[0]]
    option.value.series[1].data = [props.data[1]]
  }
)
</script>
