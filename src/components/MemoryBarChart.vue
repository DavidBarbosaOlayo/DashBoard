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
import { ref, onMounted, nextTick } from 'vue'
import ApexChart from 'vue3-apexcharts'
import type { ApexOptions } from 'apexcharts'

// 1. Define tus servidores
const servers = ['srv-01', 'srv-02', 'srv-03', 'srv-04', 'srv-05']

// 2. Simula datos realistas para cada nodo
//    (%) Aplicaciones, Buffer/Cache, Kernel, Libre (suma 100)
function generateServerMetrics() {
  return servers.map(() => {
    const app   = Math.floor(Math.random()*50) + 30  // 30–80%
    const cache = Math.floor(Math.random()*20) + 10  // 10–30%
    const kern  = Math.floor(Math.random()*10) + 5   // 5–15%
    const free  = 100 - (app + cache + kern)
    return [app, cache, kern, free]
  })
}
const matrix = generateServerMetrics()

// 3. Series apiladas: cada “capa” es una categoría de memoria
const categories = ['Aplicaciones','Buffer/Cache','Kernel','Libre']
const chartSeries = ref(
  categories.map((name, i) => ({
    name,
    data: matrix.map(row => row[i])
  }))
)

// 4. Opciones con stacking al 100%
const chartOptions = ref<ApexOptions>({
  chart: {
    id: 'memory-by-server',
    type: 'bar',
    stacked: true,
    stackType: '100%',
    toolbar: { show: false },
    fontFamily: 'Inter, sans-serif'
  },
  plotOptions: {
    bar: {
      horizontal: false,
      borderRadius: 6,
      columnWidth: '50%'
    }
  },
  colors: ['#6366f1','#10b981','#f59e0b','#e5e7eb'],
  xaxis: {
    categories: servers,
    axisBorder: { show: false },
    axisTicks: { show: false }
  },
  yaxis: {
    max: 100,
    labels: { formatter: v => `${v}%` }
  },
  dataLabels: {
    enabled: true,
    formatter: v => `${v}%`,
    style: { fontSize: '12px', fontWeight: 600 }
  },
  tooltip: {
    theme: 'dark',
    y: { formatter: v => `${v}%` }
  },
  legend: {
    position: 'bottom',
    horizontalAlign: 'center'
  },
  grid: {
    borderColor: '#e5e7eb',
    strokeDashArray: 4,
    padding: { top: 10, right: 0, bottom: 0, left: 10 }
  }
})

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
