<template>
  <ion-page>
    <!-- Header con diseño mejorado -->
    <ion-header class="ion-no-border">
      <ion-toolbar class="custom-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light"></ion-menu-button>
        </ion-buttons>
        <ion-title class="ion-text-center">
          <div class="header-title">
            <ion-icon :icon="codeSlashOutline" class="header-icon"></ion-icon>
            <span>Técnico</span>
          </div>
        </ion-title>
        <ion-buttons slot="end">
          <ion-button @click="navigateToKpis">
            <ion-icon slot="icon-only" :icon="statsChartOutline" color="light"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <!-- Deshabilitamos scroll horizontal y vertical -->
    <ion-content class="dashboard-content">
      <ion-grid>
        <!-- Fila 1 - KPIs mini-card (SparkLine) -->
        <ion-row class="ion-row-1">
          <ion-col size="12" size-lg="4">
            <SparkLine v-bind="sparkData1" />
          </ion-col>
          <ion-col size="6" size-lg="4">
            <SparkLine v-bind="sparkData2" />
          </ion-col>
          <ion-col size="6" size-lg="4">
            <SparkLine v-bind="sparkData3" />
          </ion-col>
        </ion-row>

        <!-- Fila 2 - Uso de CPU en tiempo real + gauge -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="chart-card">
              <div class="card-header">
                <h3>Uso de CPU</h3>
              </div>
              <EchartsGauge :value="currentCpu" title="CPU (%)" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="chart-card">
              <div class="card-header">
                <h3>Actividad CPU en tiempo real</h3>
                <div class="badge info">
                  <ion-icon :icon="pulseOutline"></ion-icon>
                  <span>En vivo</span>
                </div>
              </div>
              <ApexLineRT :series="seriesLoad" title="Uso CPU (%)" :kpi-target="75" color="#3b82f6" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3 - Métricas sistema -->
        <ion-row class="ion-row-3">
          <!-- Temperatura CPU -->
          <ion-col size="12" size-lg="4.5">
            <div class="chart-card">
              <div class="card-header">
                <h3>Temperatura CPU</h3>
                <div class="badge danger">
                  <ion-icon :icon="hardwareChipOutline"></ion-icon>
                  <span>{{ latestTemp }}°C</span>
                </div>
              </div>
              <CpuLineChart
                :data="cpuTempData"
                :labels="tempLabels"
                title="Temp. CPU (°C)"
                color="#ef4444"
              />
            </div>
          </ion-col>

          <ion-col size="12" size-lg="4.5">
            <div class="chart-card">
              <div class="card-header">
                <h3>Memoria</h3>
                <div class="badge warning">
                  <ion-icon :icon="serverOutline"></ion-icon>
                  <span>85%</span>
                </div>
              </div>
              <MemoryBarChart :data="memoryData" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="3">
            <div class="chart-card">
              <div class="card-header">
                <h3>Distribución</h3>
              </div>
              <DistributionPieChart :data="distributionData" title="Plataformas" />
            </div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonButtons, IonMenuButton, IonButton, IonIcon,
  IonGrid, IonRow, IonCol
} from '@ionic/vue'
import {
  codeSlashOutline,
  statsChartOutline,
  pulseOutline,
  hardwareChipOutline,
  serverOutline
} from 'ionicons/icons'
import merge from 'lodash/merge'

// Importar componentes
import SparkLine from '@/components/SparkLine.vue'
import ApexLineRT from '@/components/ApexLineRT.vue'
import EchartsGauge from '@/components/GaugeChart.vue'
import CpuLineChart from '@/components/CpuChart.vue'
import MemoryBarChart from '@/components/MemoryBarChart.vue'
import DistributionPieChart from '@/components/DistributionPieChart.vue'

// Navigator
const router = useRouter()
const navigateToKpis = () => {
  router.push('/kpis')
}

// Datos para SparkLine
const dataLogins = [120, 150, 110, 95, 140, 180, 170, 200, 220, 260]
const dataViews  = [2300, 1550, 2480, 650, 2790, 2010, 3180, 3075, 2390, 3450]
const dataErrors = [59, 49, 47, 35, 30, 28, 34, 28, 35, 42]

// Generar etiquetas de los últimos 10 días
const days = Array.from({ length: 10 }, (_, i) => {
  const d = new Date()
  d.setDate(d.getDate() - (9 - i))
  return `${String(d.getDate()).padStart(2,'0')}/${String(d.getMonth()+1).padStart(2,'0')}`
})

// Base de opciones comunes para sparklines
const sparkBaseOptions = {
  chart: {
    height: 60,
    sparkline: { enabled: true },
    toolbar: { show: false },
    dropShadow: { enabled: true, top: 1, left: 0, blur: 4, opacity: 0.2 },
    animations: { enabled: true, easing: 'easeinout', speed: 500 },
    margin: { top: 2, right: 2, bottom: 2, left: 2 }
  },
  stroke: { curve: 'smooth', width: 3 },
  fill: {
    type: 'gradient',
    gradient: {
      shade: 'light',
      shadeIntensity: 1,
      opacityFrom: 0.9,
      opacityTo: 0.5,
      stops: [0,80,100]
    }
  },
  markers: {
    size: 3,
    colors: ['#fff'],
    strokeWidth: 1,
    strokeColors: '#10b981',
    hover: { size: 5 }
  },
  grid: {
    show: true,
    borderColor: 'rgba(255,255,255,0.2)',
    strokeDashArray: 3,
    xaxis: { lines: { show: false } },
    yaxis: { lines: { show: true } }
  },
  xaxis: { categories: days, labels: { show: false } },
  yaxis: { labels: { show: false } },
  tooltip: {
    theme: 'dark',
    x: { show: true, formatter: (v:string) => `Día: ${v}` },
    y: { formatter: (v:number) => `${v}` }
  },
  colors: ['#fff']
}

// Configuración de los tres sparklines
const sparkData1 = ref({
  title: 'LOGINS (Últimos 10 días)',
  value: String(Math.floor(dataLogins.reduce((a,b)=>a+b,0)/dataLogins.length)),
  bgColor: 'gradient-blue',
  iconName: 'log-in-outline',
  chartOptions: merge({}, sparkBaseOptions, {
    chart: { id: 'logins-daily', type: 'area' },
    yaxis: { min: 0, max: Math.max(...dataLogins) * 1.1 },
    tooltip: { y: { formatter: (v:number) => `${v} logins` } }
  }),
  chartSeries: [{ name: 'Logins diarios', data: dataLogins }]
})

const sparkData2 = ref({
  title: 'WEB VIEWS (Últimos 10 días)',
  value: String(Math.floor(dataViews.reduce((a,b)=>a+b,0)/dataViews.length)),
  bgColor: 'gradient-pink',
  iconName: 'eye-outline',
  chartOptions: merge({}, sparkBaseOptions, {
    chart: { id: 'views-daily', type: 'area' },
    yaxis: { min: 0, max: Math.max(...dataViews) * 1.1 },
    tooltip: { y: { formatter: (v:number) => `${v} views` } }
  }),
  chartSeries: [{ name: 'Vistas diarias', data: dataViews }]
})

const sparkData3 = ref({
  title: 'ERRORES (Últimos 10 días)',
  value: String(Math.floor(dataErrors.reduce((a,b)=>a+b,0)/dataErrors.length)),
  bgColor: 'gradient-green',
  iconName: 'bug-outline',
  chartOptions: merge({}, sparkBaseOptions, {
    chart: { id: 'errors-daily', type: 'line' },
    stroke: { curve: 'straight', width: 2 },
    markers: { size: 0 },
    fill: { opacity: 0 },
    yaxis: { min: 0, max: Math.max(...dataErrors) * 1.1 },
    tooltip: { y: { formatter: (v:number) => `${v} errores` } }
  }),
  chartSeries: [{ name: 'Errores diarios', data: dataErrors }]
})

// --- Métricas de uso de servidores en tiempo real ---
const UPDATE_MS = 2000
const MAX_POINTS = 30
let lastX = Date.now()
const serverLoadData = ref<{ x: number; y: number }[]>([])
const seriesLoad = ref([{ name: 'CPU (%)', data: serverLoadData.value }])
const currentCpu = ref(0)
let intervalId: ReturnType<typeof setInterval>

function tick() {
  const x = lastX + UPDATE_MS
  const y = Math.floor(Math.random() * 70) + 20
  if (serverLoadData.value.length >= MAX_POINTS) serverLoadData.value.shift()
  serverLoadData.value.push({ x, y })
  lastX = x
  seriesLoad.value = [{ name: 'CPU (%)', data: [...serverLoadData.value] }]
  currentCpu.value = y
}

onMounted(() => {
  const now = Date.now()
  for (let i = 0; i < 10; i++) {
    serverLoadData.value.push({
      x: now - (10 - i) * UPDATE_MS,
      y: Math.floor(Math.random() * 70) + 20
    })
  }
  intervalId = setInterval(tick, UPDATE_MS)
})

onUnmounted(() => {
  clearInterval(intervalId)
})

// --- Nuevos datos para Temperatura CPU ---
const tempLabels = Array.from({ length: 24 }, (_, i) =>
  `${String(i).padStart(2, '0')}:00`
)
const cpuTempData = ref<number[]>([])
const latestTemp = ref<number>(0)

onMounted(() => {
  // Simulación de 24 lecturas entre 40°C y 80°C
  const simulated: number[] = tempLabels.map(() =>
    Math.floor(Math.random() * 40) + 40
  )
  cpuTempData.value = simulated
  latestTemp.value = simulated[simulated.length - 1]
})
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
  overflow: hidden !important;
}

ion-grid {
  height: 100%;
  padding: 0;
}

ion-row {
  margin-bottom: 10px;
}

ion-col {
  --ion-grid-column-padding: 8px;
}

/* Filas con alturas específicas */
@media (min-width: 992px) {
  .ion-row-1 {
    height: 16%;
    max-height: 16%;
  }
  .ion-row-2 {
    height: 38%;
    max-height: 38%;
    min-height: 200px;
  }
  .ion-row-3 {
    height: 46%;
    max-height: 46%;
  }
}

/* Pantallas más pequeñas */
@media (max-width: 991px) {
  .ion-row-2 > ion-col {
    height: 300px;
  }
}

/* Tarjetas de gráficos con tema oscuro */
.chart-card {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 16px;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
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

/* Badges con tema oscuro */
.badge {
  display: flex;
  align-items: center;
  padding: 6px 10px;
  border-radius: 16px;
  font-size: 12px;
  font-weight: 600;
  gap: 4px;
}

.badge ion-icon {
  font-size: 14px;
}

.badge.success {
  background: rgba(16, 185, 129, 0.2);
  color: #34d399;
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.badge.warning {
  background: rgba(245, 158, 11, 0.2);
  color: #fbbf24;
  border: 1px solid rgba(245, 158, 11, 0.3);
}

.badge.info {
  background: rgba(59, 130, 246, 0.2);
  color: #60a5fa;
  border: 1px solid rgba(59, 130, 246, 0.3);
  box-shadow: 0 0 10px rgba(59, 130, 246, 0.2);
}

.badge.danger {
  background: rgba(239, 68, 68, 0.2);
  color: #f87171;
  border: 1px solid rgba(239, 68, 68, 0.3);
  box-shadow: 0 0 10px rgba(239, 68, 68, 0.2);
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

/* Gauge específico */
.chart-card ::v-deep .gauge-text {
  fill: #f1f5f9 !important;
}

.chart-card ::v-deep .gauge-value {
  fill: #3b82f6 !important;
}

/* Responsive */
@media (max-width: 768px) {
  .chart-card {
    height: 250px;
  }
}
</style>