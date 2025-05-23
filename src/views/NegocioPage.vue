<template>
  <ion-page>
    <!-- Header con diseño mejorado -->
    <ion-header class="ion-no-border">
      <ion-toolbar class="custom-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light" />
        </ion-buttons>
        <ion-title class="ion-text-center">
          <div class="header-title">
            <ion-icon :icon="rocketOutline" class="header-icon" />
            <span>Negocio</span>
          </div>
        </ion-title>
      </ion-toolbar>
    </ion-header>

    <!-- Contenido principal -->
    <ion-content class="dashboard-content">
      <!-- Resumen de KPIs -->
      <div class="kpi-summary">
        <div class="kpi-card" v-for="(kpi, i) in kpis" :key="i">
          <div :class="['kpi-icon', kpi.key]">
            <ion-icon :icon="kpi.icon" />
          </div>
          <div class="kpi-data">
            <div class="kpi-value">{{ kpi.value }}</div>
            <div class="kpi-label">{{ kpi.label }}</div>
          </div>
          <div :class="['kpi-trend', kpi.trend > 0 ? 'positive' : 'negative']">
            <ion-icon :icon="kpi.trend > 0 ? trendingUpOutline : trendingDownOutline" />
            <span>{{ (kpi.trend > 0 ? '+' : '') + kpi.trend + '%' }}</span>
          </div>
        </div>
      </div>

      <!-- Grid de gráficos -->
      <div class="charts-grid">
        <!-- Fila 1 -->
        <div class="chart-row">
          <div class="chart-card map-card">
            <div class="card-header">
              <h3>Descargas por país</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal" />
              </ion-button>
            </div>
            <WorldMapChart
              :data="mapData"
              :unselected-fill="'#475569'"
            />
          </div>
          <div class="chart-card">
            <div class="card-header">
              <h3>Tareas registradas</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal" />
              </ion-button>
            </div>
            <BarChart
              :categories="barCategories"
              :series="barSeries"
              color="#3b82f6"
            />
          </div>
        </div>

        <!-- Fila 2 -->
        <div class="chart-card full-width">
          <div class="card-header">
            <h3>Evolución nº recintos</h3>
            <div class="time-selector">
              <ion-segment v-model="selectedPeriod" mode="ios">
                <ion-segment-button value="week">
                  <ion-label>Semana</ion-label>
                </ion-segment-button>
                <ion-segment-button value="month">
                  <ion-label>Mes</ion-label>
                </ion-segment-button>
                <ion-segment-button value="year">
                  <ion-label>Año</ion-label>
                </ion-segment-button>
              </ion-segment>
            </div>
          </div>
          <LineChart
            :categories="lineCategoriesComputed"
            :series="lineSeriesComputed"
            color="#10b981"
          />
        </div>

        <!-- Fila 3 -->
        <div class="chart-row">
          <div class="chart-card">
            <div class="card-header">
              <h3>Actividades mensuales</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal" />
              </ion-button>
            </div>
            <DonutChart
              :labels="donutLabels"
              :values="donutValues"
              :colors="donutColors"
            />
          </div>
          <div class="chart-card">
            <div class="card-header">
              <h3>Visión general de KPIs</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal" />
              </ion-button>
            </div>
              <RadarChart
      :indicators="radarIndicators"
      :data="radarData"
      :colors="['#3b82f6', '#10b981']"
      :lineStyles="[
        { width: 2, dash: [] },         
        { width: 2, dash: [4, 4] }      
      ]"
            />
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { defineAsyncComponent } from 'vue'
import {
  rocketOutline,
  notificationsOutline,
  peopleOutline,
  downloadOutline,
  cashOutline,
  trendingUpOutline,
  trendingDownOutline,
  ellipsisHorizontal
} from 'ionicons/icons'
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButtons,
  IonMenuButton,
  IonButton,
  IonIcon,
  IonSegment,
  IonSegmentButton,
  IonLabel
} from '@ionic/vue'

const BarChart = defineAsyncComponent(() => import('@/components/BarChart.vue'))
const LineChart = defineAsyncComponent(() => import('@/components/LineChart.vue'))
const DonutChart = defineAsyncComponent(() => import('@/components/DonutChart.vue'))
const WorldMapChart = defineAsyncComponent(() => import('@/components/WorldMapChart.vue'))
const RadarChart = defineAsyncComponent(() => import('@/components/RadarChart.vue'))

// --- Datos KPI ---
const kpis = [
  { key: 'users', icon: peopleOutline, value: '38.2K', label: 'Usuarios', trend: 12 },
  { key: 'downloads', icon: downloadOutline, value: '165.8K', label: 'Descargas', trend: 23 },
  { key: 'revenue', icon: cashOutline, value: '€232.5K', label: 'Ingresos', trend: -5 }
]

// --- Datos para barra ---
const barCategories = ['Nov', 'Dic', 'Ene', 'Feb', 'Mar', 'Abr', 'May']
const barSeries = ref([{ name: 'Tareas registradas', data: [14100, 15682, 26490, 28485, 29132, 30874, 29950] }])

// --- Selector periodo para línea ---
const selectedPeriod = ref<'week'|'month'|'year'>('month')

// Datos base para línea (mes)
const lineData = {
  week: {
    categories: ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sáb', 'Dom'],
    series: [ { name: 'Recintos activos', data: [320, 450, 512, 610, 590, 680, 720] } ]
  },
  month: {
    categories: ['Nov', 'Dic', 'Ene', 'Feb', 'Mar', 'Abr', 'May'],
    series: [ { name: 'Recintos activos', data: [513, 1055, 1483, 2006, 2265, 1970, 2375] } ]
  },
  year: {
    categories: ['2021','2022','2023','2024','2025'],
    series: [ { name: 'Recintos activos', data: [1200, 1800, 2400, 3000, 3600] } ]
  }
}

const lineCategoriesComputed = computed(() => lineData[selectedPeriod.value].categories)
const lineSeriesComputed = computed(() => lineData[selectedPeriod.value].series)

// --- Datos para donut ---
const donutLabels = ['España', 'Francia', 'Italia', 'Alemania', 'Portugal']
const donutValues = [2394, 2277, 1221, 992, 770]
const donutColors = ['#3b82f6', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6']

// --- Datos para radar ---
interface Indicator { name: string; max: number }
const radarIndicators = ref<Indicator[]>([
  { name: 'Usuarios nuevos', max: 100 },
  { name: 'Descargas', max: 100 },
  { name: 'Recintos activos', max: 100 },
  { name: 'Tareas completadas', max: 100 },
  { name: 'Cobertura geográfica', max: 100 }
])
const radarData = ref([
  { name: 'Uso actual', value: [41, 77, 55, 53, 39] },
  { name: 'Objetivo',   value: [90, 95, 85, 90, 80] }
])

// --- Datos aleatorios para el mapa ---
const mapData = ref([
  { country: 'España',       value: 12896 },
  { country: 'Francia',      value: 11611 },
  { country: 'Alemania',     value:  9960 },
  { country: 'Italia',       value:  7645 },
  { country: 'Portugal',     value:  5312 },
  { country: 'Países Bajos', value:  3308 },
  { country: 'Polonia',      value:  1024 },
  { country: 'Suecia',       value:  512 },
  { country: 'Bélgica',      value:  256 },
  { country: 'República Checa', value: 128 }
])
</script>

<style scoped>
/* Header */
.custom-toolbar {
  --background: linear-gradient(135deg, #1e293b, #0f172a);
  --color: #f8fafc;
  padding: 5px 0;
  box-shadow: 0 2px 8px rgba(0,0,0,0.3);
}

.header-title {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-weight: 600;
}

.header-icon {
  font-size: 20px;
}

/* Contenido principal */
.dashboard-content {
  --background: #0f172a;
  --padding-top: 8px;
  --padding-bottom: 8px;
  --padding-start: 8px;
  --padding-end: 8px;
  overflow: hidden;
}

/* KPI Summary */
.kpi-summary {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 12px;
  margin-bottom: 12px;
}

.kpi-card {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 16px;
  display: flex;
  align-items: center;
  transition: transform 0.3s, box-shadow 0.3s;
  position: relative;
  overflow: hidden;
}

.kpi-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.02);
  z-index: -1;
}

.kpi-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 16px rgba(0,0,0,0.2);
}

.kpi-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 16px;
}

.kpi-icon ion-icon {
  font-size: 24px;
  color: white;
}

.kpi-icon.users {
  background: #3b82f6;
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.4);
}

.kpi-icon.downloads {
  background: #10b981;
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.4);
}

.kpi-icon.revenue {
  background: #f59e0b;
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.4);
}

.kpi-data {
  flex: 1;
}

.kpi-value {
  font-size: 20px;
  font-weight: 700;
  color: #f1f5f9;
  line-height: 1.2;
}

.kpi-label {
  font-size: 13px;
  color: #94a3b8;
}

.kpi-trend {
  display: flex;
  align-items: center;
  font-size: 13px;
  font-weight: 600;
  padding: 6px 10px;
  border-radius: 16px;
}

.kpi-trend ion-icon {
  margin-right: 4px;
}

.kpi-trend.positive {
  color: #34d399;
  background: rgba(16, 185, 129, 0.2);
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.kpi-trend.negative {
  color: #f87171;
  background: rgba(239, 68, 68, 0.2);
  border: 1px solid rgba(239, 68, 68, 0.3);
}

/* Charts Grid */
.charts-grid {
  display: flex;
  flex-direction: column;
  gap: 12px;
  height: calc(100% - 100px);
}

.chart-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px,1fr));
  gap: 12px;
  flex: 1;
}

.chart-card {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  height: 100%;
  position: relative;
}

.chart-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.02);
  z-index: -1;
}

.chart-card.full-width {
  grid-column: 1 / -1;
  height: 25%;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.card-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: #f1f5f9;
}

.card-header ion-button {
  --color: #94a3b8;
}

.card-header ion-button:hover {
  --color: #f1f5f9;
}

/* Time Selector */
.time-selector {
  max-width: 280px;
}

ion-segment {
  --background: rgba(255, 255, 255, 0.05);
  border-radius: 8px;
  overflow: hidden;
}

ion-segment-button {
  --color: #94a3b8;
  --color-checked: #f1f5f9;
  --indicator-color: transparent;
  --background-checked: rgba(59, 130, 246, 0.3);
  --border-radius: 6px;
  font-size: 13px;
  margin: 2px;
}

/* Mapa */
.map-card ::v-deep .echarts-layer-map path {
  fill: #475569 !important;
}

/* Estilos globales para gráficos - Textos claros */
.chart-card ::v-deep .apexcharts-text {
  fill: #e2e8f0 !important;
}

.chart-card ::v-deep .apexcharts-xaxis-label,
.chart-card ::v-deep .apexcharts-yaxis-label {
  fill: #cbd5e1 !important;
}

.chart-card ::v-deep .apexcharts-gridline {
  stroke: rgba(255, 255, 255, 0.1) !important;
}

.chart-card ::v-deep .apexcharts-legend-text {
  color: #e2e8f0 !important;
}

.chart-card ::v-deep .apexcharts-tooltip {
  background: rgba(15, 23, 42, 0.95) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  color: #f1f5f9 !important;
}

/* ECharts - Textos claros */
.chart-card ::v-deep .echarts-tooltip {
  background-color: rgba(15, 23, 42, 0.95) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  color: #f1f5f9 !important;
}

.chart-card ::v-deep text {
  fill: #cbd5e1 !important;
}

.chart-card ::v-deep .echarts-legend text {
  fill: #e2e8f0 !important;
}

/* Leyendas específicas para cada tipo de gráfico */
/* Mapa - Leyenda */
.chart-card ::v-deep .echarts-visualMap-text {
  fill: #f1f5f9 !important;
}

.chart-card ::v-deep .echarts-visualMap-continuous text {
  fill: #f1f5f9 !important;
}

/* Radar - Leyenda */
.chart-card ::v-deep .echarts-legend-item-name {
  fill: #f1f5f9 !important;
  color: #f1f5f9 !important;
}

.chart-card ::v-deep .echarts-radar-name {
  fill: #f1f5f9 !important;
}

.chart-card ::v-deep .echarts-radar-indicator-name {
  fill: #f1f5f9 !important;
}

/* Donut - Leyenda */
.chart-card ::v-deep .apexcharts-legend-series {
  color: #f1f5f9 !important;
}

.chart-card ::v-deep .apexcharts-legend-text {
  color: #f1f5f9 !important;
}

.chart-card ::v-deep .apexcharts-datalabel-label,
.chart-card ::v-deep .apexcharts-datalabel-value {
  fill: #f1f5f9 !important;
}

/* Línea de tiempo real - Leyenda */
.chart-card ::v-deep .apexcharts-legend-marker {
  margin-right: 5px !important;
}

/* Línea y barra - Leyendas */
.chart-card ::v-deep .apexcharts-legend {
  background: transparent !important;
}

.chart-card ::v-deep .apexcharts-legend-series {
  margin: 5px !important;
}

/* Tooltips mejorados */
.chart-card ::v-deep .apexcharts-tooltip-title {
  background: rgba(30, 41, 59, 0.95) !important;
  color: #f1f5f9 !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
}

.chart-card ::v-deep .apexcharts-tooltip-series-group {
  background: rgba(15, 23, 42, 0.95) !important;
  color: #f1f5f9 !important;
}

.chart-card ::v-deep .apexcharts-tooltip-marker {
  margin-right: 5px !important;
}

.chart-card ::v-deep .apexcharts-tooltip-y-group {
  padding: 4px 0 !important;
}

/* Responsive */
@media (max-width: 768px) {
  .kpi-summary {
    grid-template-columns: 1fr;
  }
  
  .chart-card {
    height: 250px;
  }
  
  .chart-row {
    grid-template-columns: 1fr;
  }
}
</style>