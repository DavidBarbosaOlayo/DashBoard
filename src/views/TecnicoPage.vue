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

        <!-- Fila 2 - Realtime usuarios + gauge -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="chart-card">
              <div class="card-header">
                <h3>Usuarios online</h3>
              </div>
              <EchartsGauge :value="currentUsers" title="Usuarios" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="chart-card">
              <div class="card-header">
                <h3>Actividad en tiempo real</h3>
                <div class="badge info">
                  <ion-icon :icon="pulseOutline"></ion-icon>
                  <span>En vivo</span>
                </div>
              </div>
              <ApexLineRT :series="seriesRT" title="Usuarios online" :kpi-target="70" color="#6366f1" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3 - Métricas sistema -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4.5">
            <div class="chart-card">
              <div class="card-header">
                <h3>Carga CPU</h3>
                <div class="badge success">
                  <ion-icon :icon="hardwareChipOutline"></ion-icon>
                  <span>Normal</span>
                </div>
              </div>
              <CpuLineChart :data="cpuData" :labels="cpuLabels" color="#6366f1" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4.5">
            <div class="chart-card">
              <div class="card-header">
                <h3>Memoria</h3>
                <div class="badge warning">
                  <ion-icon :icon="serverOutline"></ion-icon>
                  <span>65%</span>
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
  codeSlashOutline, statsChartOutline, pulseOutline,
  hardwareChipOutline, serverOutline, analyticsOutline,
  barChartOutline, pieChartOutline
} from 'ionicons/icons'

// Importar componentes
import SparkLine from '@/components/SparkLine.vue'
import ApexLineRT from '@/components/ApexLineRT.vue'
import EchartsGauge from '@/components/GaugeChart.vue'
import CpuLineChart from '@/components/CpuChart.vue'
import MemoryBarChart from '@/components/MemoryBarChart.vue'
import DistributionPieChart from '@/components/DistributionPieChart.vue'

// Router para navegación
const router = useRouter()

// Función para navegar a la página de KPIs
const navigateToKpis = () => {
  router.push('/kpis')
}

// Datos para SparkLine
const sparkData1 = ref({
  title: 'CLICKS',
  value: '1,234',
  bgColor: 'gradient-blue',
  iconName: 'navigate-outline',
  chartOptions: {
    chart: {
      id: 'clicks',
      type: 'area',
      sparkline: { enabled: true },
      toolbar: { show: false }
    },
    stroke: { curve: 'smooth', width: 2 },
    fill: {
      type: 'gradient',
      gradient: {
        shadeIntensity: 1,
        opacityFrom: 0.7,
        opacityTo: 0.3,
        stops: [0, 90, 100]
      }
    },
    colors: ['#fff']
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
})

const sparkData2 = ref({
  title: 'VIEWS',
  value: '1,982',
  bgColor: 'gradient-pink',
  iconName: 'eye-outline',
  chartOptions: {
    chart: {
      id: 'views',
      type: 'bar',
      sparkline: { enabled: true },
      toolbar: { show: false }
    },
    stroke: { width: 0 },
    fill: { opacity: 1 },
    colors: ['#fff']
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
})

const sparkData3 = ref({
  title: 'LEADS',
  value: '2,011',
  bgColor: 'gradient-orange',
  iconName: 'people-outline',
  chartOptions: {
    chart: {
      id: 'leads',
      type: 'line',
      sparkline: { enabled: true },
      toolbar: { show: false }
    },
    stroke: { curve: 'straight', width: 3 },
    colors: ['#fff']
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
})

// Datos para gráfico en tiempo real
const UPDATE_MS = 2000 // Cambiado de 1000 a 2000 para mejor rendimiento
const MAX_POINTS = 30 // Reducido de 60 a 30 para mejor rendimiento
let lastX = Date.now()
const dataRT = ref<{x: number; y: number}[]>([])
const seriesRT = ref([{ name: 'Usuarios', data: dataRT.value }])

// Valor para el gauge
const currentUsers = ref(0)

// Datos para el gráfico de CPU (ChartJS)
const cpuData = ref([28, 45, 35, 55, 40, 65, 80, 74, 68, 85, 75, 62])
const cpuLabels = ref(['8:00', '9:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00', '18:00', '19:00'])

// Datos para el gráfico de memoria (ApexCharts)
const memoryData = ref([
  { name: 'App', value: 35, color: '#6366f1' },
  { name: 'Sistema', value: 25, color: '#10b981' },
  { name: 'Libre', value: 40, color: '#f59e0b' }
])

// Datos para el gráfico de distribución (ECharts)
const distributionData = ref([
  { name: 'iOS', value: 40, itemStyle: { color: '#6366f1' } },
  { name: 'Android', value: 35, itemStyle: { color: '#10b981' } },
  { name: 'Web', value: 25, itemStyle: { color: '#f59e0b' } }
])

// Lógica para actualizar datos en tiempo real
let interval: ReturnType<typeof setInterval> | undefined

function tick() {
  // Genera punto aleatorio
  const x = lastX + UPDATE_MS
  const y = Math.floor(Math.random() * 90) + 10
  
  // Limita el número de puntos ANTES de añadir el nuevo punto
  if (dataRT.value.length >= MAX_POINTS) {
    dataRT.value.shift()
  }
  
  // Añade el nuevo punto
  dataRT.value.push({ x, y })
  lastX = x
  
  // Actualiza la serie con una nueva referencia para forzar la actualización
  seriesRT.value = [{ 
    name: 'Usuarios', 
    data: [...dataRT.value] // Crear una nueva referencia del array
  }]
  
  // Actualiza el gauge
  currentUsers.value = y
}

onMounted(() => {
  // Iniciar con algunos datos
  const now = Date.now()
  for (let i = 0; i < 10; i++) {
    const x = now - (10 - i) * UPDATE_MS
    const y = Math.floor(Math.random() * 90) + 10
    dataRT.value.push({ x, y })
  }
  
  // Iniciar intervalo
  interval = setInterval(tick, UPDATE_MS)
})

onUnmounted(() => {
  if (interval) {
    clearInterval(interval)
  }
})
</script>

<style scoped>
/* Estilos para el header */
.custom-toolbar {
  --background: var(--ion-color-primary);
  --color: white;
  padding: 5px 0;
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

/* Estilos para el contenido */
.dashboard-content {
  --background: #f9fafb;
  --padding-top: 8px;
  --padding-bottom: 8px;
  --padding-start: 8px;
  --padding-end: 8px;
  overflow: hidden; /* Evitar scroll */
}

ion-grid {
  height: 100%;
  padding: 0;
}

ion-row {
  margin-bottom: 10px; /* Reducido de 20px a 10px */
}

ion-col {
  --ion-grid-column-padding: 8px; /* Reducido de 10px a 8px */
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

/* Añadir estilos para pantallas más pequeñas */
@media (max-width: 991px) {
  .ion-row-2 > ion-col {
    height: 300px; /* Altura fija para los contenedores en pantallas pequeñas */
  }
}

/* Tarjetas de gráficos */
.chart-card {
  background: white;
  border-radius: 12px;
  padding: 10px; /* Reducido de 16px a 10px */
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  position: relative;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px; /* Reducido de 16px a 8px */
}

.card-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: var(--ion-color-dark);
}

/* Badges */
.badge {
  display: flex;
  align-items: center;
  padding: 4px 8px;
  border-radius: 16px;
  font-size: 12px;
  font-weight: 600;
  gap: 4px;
}

.badge ion-icon {
  font-size: 14px;
}

.badge.success {
  background-color: rgba(16, 185, 129, 0.1);
  color: #10b981;
}

.badge.warning {
  background-color: rgba(245, 158, 11, 0.1);
  color: #f59e0b;
}

.badge.info {
  background-color: rgba(99, 102, 241, 0.1);
  color: #6366f1;
}

.badge.danger {
  background-color: rgba(239, 68, 68, 0.1);
  color: #ef4444;
}
</style>