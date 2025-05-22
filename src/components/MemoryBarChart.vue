<template>
    <div class="chart-container">
      <ApexChart
        type="bar"
        height="100%"
        :options="chartOptions"
        :series="chartSeries"
      />
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, watch, onMounted, nextTick } from 'vue'
  import ApexChart from 'vue3-apexcharts'
  import type { ApexOptions } from 'apexcharts'
  
  interface Props {
    data?: { name: string; value: number; color: string }[]
    title?: string
    maxValue?: number
  }
  
  const props = withDefaults(defineProps<Props>(), {
    data: () => [
      { name: 'App', value: 35, color: '#6366f1' },
      { name: 'Sistema', value: 25, color: '#10b981' },
      { name: 'Libre', value: 40, color: '#f59e0b' }
    ],
    title: 'Uso de memoria',
    maxValue: 100
  })
  
  // Preparar datos para ApexCharts
  const chartSeries = ref([{
    name: 'Memoria',
    data: props.data.map(item => item.value)
  }])
  
  const chartOptions = ref<ApexOptions>({
    chart: {
      id: 'memory-bar',
      type: 'bar',
      foreColor: '#374151',
      background: 'transparent',
      toolbar: { show: false },
      fontFamily: 'Inter, sans-serif',
      animations: {
        enabled: true,
        easing: 'easeinout',
        speed: 800,
        animateGradually: { enabled: true, delay: 150 },
        dynamicAnimation: { enabled: true, speed: 350 }
      }
    },
    colors: props.data.map(item => item.color),
    plotOptions: {
      bar: {
        distributed: true,
        borderRadius: 6,
        columnWidth: '60%',
        dataLabels: {
          position: 'top'
        }
      }
    },
    dataLabels: {
      enabled: true,
      formatter: (val: number) => `${val}%`,
      offsetY: -20,
      style: {
        fontSize: '12px',
        colors: ['#374151'],
        fontWeight: 600
      }
    },
    xaxis: {
      categories: props.data.map(item => item.name),
      labels: {
        style: {
          fontFamily: 'Inter, sans-serif',
          fontWeight: 500
        }
      },
      axisBorder: { show: false },
      axisTicks: { show: false }
    },
    yaxis: {
      max: props.maxValue,
      labels: {
        formatter: (val: number) => `${val}%`
      }
    },
    tooltip: {
      theme: 'dark',
      y: {
        formatter: (val: number) => `${val}%`
      }
    },
    grid: {
      borderColor: '#e5e7eb',
      strokeDashArray: 4,
      padding: { top: 20, right: 0, bottom: 0, left: 10 }
    },
    legend: {
      show: false
    },
    states: {
      hover: {
        filter: {
          type: 'darken',
          value: 0.9
        }
      },
      active: {
        filter: {
          type: 'darken',
          value: 0.85
        }
      }
    }
  })
  
  // Actualizar el gráfico cuando cambian los props
  watch(() => props.data, () => {
    chartSeries.value = [{
      name: 'Memoria',
      data: props.data.map(item => item.value)
    }]
    
    chartOptions.value = {
      ...chartOptions.value,
      colors: props.data.map(item => item.color),
      xaxis: {
        ...chartOptions.value.xaxis,
        categories: props.data.map(item => item.name)
      },
      yaxis: {
        ...chartOptions.value.yaxis,
        max: props.maxValue
      }
    }
  }, { deep: true })
  
  onMounted(async () => {
    await nextTick()
    window.dispatchEvent(new Event('resize'))
  })
  </script>
  
  <style scoped>
  .chart-container {
    width: 100%;
    height: calc(100% - 30px);
  }
  </style>