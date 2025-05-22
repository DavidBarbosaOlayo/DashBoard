<template>
    <div class="chart-container">
      <Doughnut :chart-data="chartData" :options="chartOptions" />
    </div>
  </template>
  
  <script setup lang="ts">
  import { Doughnut } from 'vue-chartjs'
  import {
    Chart,
    ArcElement,
    Tooltip,
    Legend
  } from 'chart.js'
  
  Chart.register(ArcElement, Tooltip, Legend)
  
  const props = defineProps<{
    labels: string[]
    values: number[]
  }>()
  
  const chartData = {
    labels: props.labels,
    datasets: [{
      data: props.values,
      backgroundColor: [
        '#6366f1','#10b981','#f59e0b','#ef4444','#8b5cf6'
      ],
      borderWidth: 1
    }]
  }
  
  const chartOptions = {
    responsive: true,
    maintainAspectRatio: false,
    plugins: {
      legend: { position: 'bottom' },
      tooltip: { enabled: true }
    }
  }
  </script>
  
  <style scoped>
  .chart-container {
    width: 100%;
    height: 100%;
    position: relative;
  }
  </style>
  