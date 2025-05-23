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
        <ion-buttons slot="end">
          <ion-button>
            <ion-icon slot="icon-only" :icon="notificationsOutline" color="light" />
          </ion-button>
        </ion-buttons>
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
              :unselected-fill="'#a1a1aa'"
            />
          </div>
          <div class="chart-card">
            <div class="card-header">
              <h3>Tareas semanales registradas</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal" />
              </ion-button>
            </div>
            <BarChart
              :categories="barCategories"
              :series="barSeries"
              color="var(--ion-color-primary)"
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
            color="var(--ion-color-secondary)"
          />
        </div>

        <!-- Fila 3 -->
        <div class="chart-row">
          <div class="chart-card">
            <div class="card-header">
              <h3>Actividades por países</h3>
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
  { key: 'users', icon: peopleOutline, value: '28.2K', label: 'Usuarios', trend: 12 },
  { key: 'downloads', icon: downloadOutline, value: '45.8K', label: 'Descargas', trend: 23 },
  { key: 'revenue', icon: cashOutline, value: '€132.5K', label: 'Ingresos', trend: -5 }
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
const donutValues = [350, 200, 100, 80, 70]
const donutColors = ['#6366f1', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6']

// --- Datos para radar ---
interface Indicator { name: string; max: number }
const radarIndicators = ref<Indicator[]>([
  { name: 'Usuarios nuevos', max: 100 },
  { name: 'Descargas', max: 100 },
  { name: 'Recintos activos', max: 100 },
  { name: 'Tareas completadas', max: 100 },
  { name: 'Cobertura geográfica', max: 100 }
])
const radarData = ref([{ name: 'Uso actual', value: [85, 90, 75, 80, 70] }])

// --- Datos aleatorios para el mapa ---
const countries = ['Spain','Germany','France','Italy','Netherlands','Poland','Portugal']
function getRandomInt(max: number) { return Math.floor(Math.random() * (max + 1)) }
const mapData = ref(countries.map(c => ({ country: c, value: getRandomInt(12000) })))
</script>

<style>
:root {
  --ion-color-primary: #6366f1;
  --ion-color-primary-tint: #7376f2;
  --ion-color-secondary: #10b981;
  --ion-color-secondary-tint: #28c08e;
  --ion-color-tertiary: #f59e0b;
  --ion-color-tertiary-tint: #f6a823;
  --ion-color-success: #10b981;
  --ion-color-danger: #ef4444;

  /* nuevos fondos */
  --ion-color-bg-light: #f3f4f6; /* gris claro para cards */
  --ion-color-bg-dark:  #e5e7eb; /* gris más oscuro para el lienzo */
}

.custom-toolbar {
  --background: #6B7280;
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

.dashboard-content {
  --background: var(--ion-color-bg-dark);
  --padding-top: 8px;
  --padding-bottom: 8px;
  --padding-start: 8px;
  --padding-end: 8px;
  overflow: hidden;
}

.kpi-summary {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 12px;
  margin-bottom: 12px;
}

.kpi-card {
  background: white;
  border-radius: 12px;
  padding: 12px;
  display: flex;
  align-items: center;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}
.kpi-card:hover { transform: translateY(-2px); box-shadow: 0 4px 6px rgba(0,0,0,0.1); }

.kpi-icon {
  width: 40px; height: 40px;
  border-radius: 10px;
  display: flex; align-items: center; justify-content: center;
  margin-right: 12px;
}
.kpi-icon ion-icon { font-size: 20px; color: white; }
.kpi-icon.users    { background: linear-gradient(135deg, var(--ion-color-primary), var(--ion-color-primary-tint)); }
.kpi-icon.downloads{ background: linear-gradient(135deg, var(--ion-color-secondary), var(--ion-color-secondary-tint)); }
.kpi-icon.revenue  { background: linear-gradient(135deg, var(--ion-color-tertiary), var(--ion-color-tertiary-tint)); }

.kpi-data { flex: 1; }
.kpi-value { font-size: 20px; font-weight: 700; color: #111827; line-height: 1.2; }
.kpi-label { font-size: 13px; color: #6b7280; }

.kpi-trend {
  display: flex; align-items: center;
  font-size: 13px; font-weight: 600;
  padding: 3px 6px; border-radius: 16px;
}
.kpi-trend ion-icon { margin-right: 3px; }
.kpi-trend.positive { color: var(--ion-color-success); background: rgba(16,185,129,0.1); }
.kpi-trend.negative { color: var(--ion-color-danger); background: rgba(239,68,68,0.1); }

.charts-grid {
  display: flex; flex-direction: column; gap: 12px;
  height: calc(100% - 100px);
}

.chart-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px,1fr));
  gap: 12px; flex: 1;
}

.chart-card {
  background: var(--ion-color-bg-light);
  border-radius: 12px;
  padding: 10px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  height: 100%;
}

.chart-card.full-width {
  grid-column: 1 / -1;
  height: 25%;
}

.card-header {
  display: flex; justify-content: space-between; align-items: center;
  margin-bottom: 8px;
}
.card-header h3 {
  margin: 0; font-size: 15px; font-weight: 600; color: #111827;
}

.time-selector { max-width: 280px; }

/* Mapa: territorios no pintados en gris oscuro */
.map-card ::v-deep .echarts-layer-map path {
  fill: #9ca3af !important;
}

@media (max-width: 768px) {
  .kpi-summary { grid-template-columns: 1fr; }
  .chart-card { height: 250px; }
}
</style>
