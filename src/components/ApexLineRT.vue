<template>
  <apex-chart type="line" height="100%"
              :series="series"
              :options="chartOptions" />
</template>

<script setup lang="ts">
import { computed } from 'vue'
import ApexChart from 'vue3-apexcharts'
import type { ApexAxisChartSeries, ApexOptions } from 'apexcharts'

const props = defineProps<{
  series: ApexAxisChartSeries
  title: string
  kpiTarget: number
  color: string
}>()

const chartOptions = computed<ApexOptions>(() => ({
  chart: {
    id: 'realtime',
    type: 'line',
    animations: {
      enabled: true,
      easing: 'linear',
      dynamicAnimation: {
        speed: 1000
      }
    },
    toolbar: { show: false },
    zoom: { enabled: false },
    background: 'transparent',
    fontFamily: 'Inter, sans-serif'
  },
  stroke: {
    curve: 'smooth',
    width: 3,
    colors: [props.color]
  },
  markers: {
    size: 0,
    hover: {
      size: 5
    }
  },
  xaxis: {
    type: 'datetime',
    labels: {
      style: {
        colors: '#374151',
        fontFamily: 'Inter, sans-serif'
      },
      datetimeFormatter: {
        year: 'yyyy',
        month: 'MMM \'yy',
        day: 'dd MMM',
        hour: 'HH:mm:ss'
      }
    },
    axisBorder: {
      show: false
    },
    axisTicks: {
      show: false
    }
  },
  yaxis: {
    labels: {
      style: {
        colors: '#374151',
        fontFamily: 'Inter, sans-serif'
      }
    },
    min: 0,
    max: 100,
    tickAmount: 5
  },
  tooltip: {
    x: {
      format: 'HH:mm:ss'
    },
    theme: 'dark'
  },
  grid: {
    borderColor: '#374151',
    strokeDashArray: 4,
    padding: {
      top: 0,
      right: 0,
      bottom: 0,
      left: 10
    }
  },
  title: {
    text: props.title,
    align: 'left',
    style: {
      color: '#111827',
      fontSize: '16px',
      fontFamily: 'Inter, sans-serif',
      fontWeight: 600
    }
  },
  annotations: {
    yaxis: [{
      y: props.kpiTarget,
      borderColor: '#f59e0b',
      borderWidth: 2,
      strokeDashArray: 4,
      label: {
        borderColor: '#f59e0b',
        style: {
          color: '#000000',
          background: '#f59e0b'
        },
        text: 'Objetivo'
      }
    }]
  },
  fill: {
    type: 'gradient',
    gradient: {
      shade: 'dark',
      type: 'vertical',
      shadeIntensity: 0.3,
      gradientToColors: undefined,
      inverseColors: false,
      opacityFrom: 0.8,
      opacityTo: 0.2,
      stops: [0, 90, 100]
    }
  }
}))
</script>

<style scoped>
:deep(.apexcharts-tooltip) {
  background: rgba(17, 24, 39, 0.9) !important;
  border: none !important;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1) !important;
  border-radius: 6px !important;
}

:deep(.apexcharts-tooltip-title) {
  background: rgba(31, 41, 55, 0.8) !important;
  border-bottom: 1px solid #374151 !important;
  font-family: 'Inter', sans-serif !important;
}

:deep(.apexcharts-xaxistooltip) {
  background: rgba(17, 24, 39, 0.9) !important;
  border: none !important;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1) !important;
  color: #f3f4f6 !important;
}

:deep(.apexcharts-xaxistooltip-bottom:after) {
  border-bottom-color: rgba(17, 24, 39, 0.9) !important;
}

:deep(.apexcharts-xaxistooltip-bottom:before) {
  border-bottom-color: rgba(17, 24, 39, 0.9) !important;
}
</style>
