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
            <ion-icon :icon="rocketOutline" class="header-icon"></ion-icon>
            <span>Dashboard</span>
          </div>
        </ion-title>
        <ion-buttons slot="end">
          <ion-button>
            <ion-icon slot="icon-only" :icon="notificationsOutline" color="light"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <!-- Contenido principal -->
    <ion-content class="dashboard-content">
      <!-- Resumen de KPIs -->
      <div class="kpi-summary">
        <div class="kpi-card">
          <div class="kpi-icon users">
            <ion-icon :icon="peopleOutline"></ion-icon>
          </div>
          <div class="kpi-data">
            <div class="kpi-value">1,245</div>
            <div class="kpi-label">Usuarios</div>
          </div>
          <div class="kpi-trend positive">
            <ion-icon :icon="trendingUpOutline"></ion-icon>
            <span>+12%</span>
          </div>
        </div>
        <div class="kpi-card">
          <div class="kpi-icon downloads">
            <ion-icon :icon="downloadOutline"></ion-icon>
          </div>
          <div class="kpi-data">
            <div class="kpi-value">45.8K</div>
            <div class="kpi-label">Descargas</div>
          </div>
          <div class="kpi-trend positive">
            <ion-icon :icon="trendingUpOutline"></ion-icon>
            <span>+23%</span>
          </div>
        </div>
        <div class="kpi-card">
          <div class="kpi-icon revenue">
            <ion-icon :icon="cashOutline"></ion-icon>
          </div>
          <div class="kpi-data">
            <div class="kpi-value">€32.5K</div>
            <div class="kpi-label">Ingresos</div>
          </div>
          <div class="kpi-trend negative">
            <ion-icon :icon="trendingDownOutline"></ion-icon>
            <span>-5%</span>
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
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal"></ion-icon>
              </ion-button>
            </div>
            <WorldMapChart :data="mapData" />
          </div>
          <div class="chart-card">
            <div class="card-header">
              <h3>Uso semanal</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal"></ion-icon>
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
            <h3>Evolución de hectáreas</h3>
            <div class="time-selector">
              <ion-segment value="month" mode="ios">
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
            :categories="lineCategories"
            :series="lineSeries"
            color="var(--ion-color-secondary)"
          />
        </div>

        <!-- Fila 3 -->
        <div class="chart-row">
          <div class="chart-card">
            <div class="card-header">
              <h3>Agricultores activos</h3>
              <ion-button fill="clear" size="small">
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal"></ion-icon>
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
                <ion-icon slot="icon-only" :icon="ellipsisHorizontal"></ion-icon>
              </ion-button>
            </div>
            <RadarChart :indicators="radarIndicators" :data="radarData" />
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue'
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
import { defineAsyncComponent } from 'vue'

const BarChart = defineAsyncComponent(() => import('@/components/BarChart.vue'))
const LineChart = defineAsyncComponent(() => import('@/components/LineChart.vue'))
const DonutChart = defineAsyncComponent(() => import('@/components/DonutChart.vue'))
const WorldMapChart = defineAsyncComponent(() => import('@/components/WorldMapChart.vue'))
const RadarChart = defineAsyncComponent(() => import('@/components/RadarChart.vue'))

const barCategories = ['Sem 1', 'Sem 2', 'Sem 3', 'Sem 4', 'Sem 5', 'Sem 6', 'Sem 7']
const barSeries = ref([{ name: 'Actividades', data: [320, 450, 390, 580, 610, 720, 800] }])

const lineCategories = ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul']
const lineSeries = ref([{ name: 'Hectáreas', data: [5, 12, 8, 15, 10, 18, 20] }])

const donutLabels = ['España', 'Francia', 'Italia', 'Alemania', 'Portugal']
const donutValues = [350, 200, 100, 80, 70]
const donutColors = ['#6366f1', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6']

const mapData = ref([
  { country: 'Spain', value: 16000 },
  { country: 'Germany', value: 15000 },
  { country: 'France', value: 12000 },
  { country: 'Italy', value: 9000 },
  { country: 'Netherlands', value: 8000 },
  { country: 'Poland', value: 7500 },
  { country: 'Portugal', value: 3000 }
])

interface Indicator {
  name: string
  max: number
}
const radarIndicators = ref<Indicator[]>([
  { name: 'Usuarios', max: 2000 },
  { name: 'Descargas', max: 60000 },
  { name: 'Ingresos (K€)', max: 50 },
  { name: 'Hectáreas', max: 25 },
  { name: 'Agricultores', max: 400 }
])
const radarData = ref([
  { name: 'Actual', value: [1245, 45800, 32.5, 20, 350] }
])
</script>

<style>
:root {
  --ion-color-primary: #6366f1;
  --ion-color-primary-rgb: 99, 102, 241;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #575ad4;
  --ion-color-primary-tint: #7376f2;

  --ion-color-secondary: #10b981;
  --ion-color-secondary-rgb: 16, 185, 129;
  --ion-color-secondary-contrast: #ffffff;
  --ion-color-secondary-contrast-rgb: 255, 255, 255;
  --ion-color-secondary-shade: #0ea371;
  --ion-color-secondary-tint: #28c08e;

  --ion-color-tertiary: #f59e0b;
  --ion-color-tertiary-rgb: 245, 158, 11;
  --ion-color-tertiary-contrast: #000000;
  --ion-color-tertiary-contrast-rgb: 0, 0, 0;
  --ion-color-tertiary-shade: #d88b0a;
  --ion-color-tertiary-tint: #f6a823;

  --ion-color-success: #10b981;
  --ion-color-success-rgb: 16, 185, 129;
  --ion-color-success-contrast: #ffffff;
  --ion-color-success-contrast-rgb: 255, 255, 255;
  --ion-color-success-shade: #0ea371;
  --ion-color-success-tint: #28c08e;

  --ion-color-warning: #f59e0b;
  --ion-color-warning-rgb: 245, 158, 11;
  --ion-color-warning-contrast: #000000;
  --ion-color-warning-contrast-rgb: 0, 0, 0;
  --ion-color-warning-shade: #d88b0a;
  --ion-color-warning-tint: #f6a823;

  --ion-color-danger: #ef4444;
  --ion-color-danger-rgb: 239, 68, 68;
  --ion-color-danger-contrast: #ffffff;
  --ion-color-danger-contrast-rgb: 255, 255, 255;
  --ion-color-danger-shade: #d23c3c;
  --ion-color-danger-tint: #f15757;

  --ion-color-dark: #111827;
  --ion-color-dark-rgb: 17, 24, 39;
  --ion-color-dark-contrast: #ffffff;
  --ion-color-dark-contrast-rgb: 255, 255, 255;
  --ion-color-dark-shade: #0f1522;
  --ion-color-dark-tint: #29303d;

  --ion-color-medium: #6b7280;
  --ion-color-medium-rgb: 107, 114, 128;
  --ion-color-medium-contrast: #ffffff;
  --ion-color-medium-contrast-rgb: 255, 255, 255;
  --ion-color-medium-shade: #5e6471;
  --ion-color-medium-tint: #7a808d;

  --ion-color-light: #f3f4f6;
  --ion-color-light-rgb: 243, 244, 246;
  --ion-color-light-contrast: #000000;
  --ion-color-light-contrast-rgb: 0, 0, 0;
  --ion-color-light-shade: #d6d7d9;
  --ion-color-light-tint: #f4f5f7;

  --ion-background-color: #f9fafb;
  --ion-background-color-rgb: 249, 250, 251;

  --ion-text-color: #111827;
  --ion-text-color-rgb: 17, 24, 39;

  --ion-font-family: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}
</style>

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
  --padding-top: 16px;
  --padding-bottom: 16px;
  --padding-start: 16px;
  --padding-end: 16px;
}

/* Tarjetas de KPI */
.kpi-summary {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 16px;
  margin-bottom: 20px;
}

.kpi-card {
  background: white;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  align-items: center;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.kpi-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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
  background: linear-gradient(135deg, var(--ion-color-primary), var(--ion-color-primary-tint));
}

.kpi-icon.downloads {
  background: linear-gradient(135deg, var(--ion-color-secondary), var(--ion-color-secondary-tint));
}

.kpi-icon.revenue {
  background: linear-gradient(135deg, var(--ion-color-tertiary), var(--ion-color-tertiary-tint));
}

.kpi-data {
  flex: 1;
}

.kpi-value {
  font-size: 24px;
  font-weight: 700;
  color: var(--ion-color-dark);
  line-height: 1.2;
}

.kpi-label {
  font-size: 14px;
  color: var(--ion-color-medium);
}

.kpi-trend {
  display: flex;
  align-items: center;
  font-size: 14px;
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 16px;
}

.kpi-trend ion-icon {
  margin-right: 4px;
}

.kpi-trend.positive {
  color: var(--ion-color-success);
  background-color: rgba(var(--ion-color-success-rgb), 0.1);
}

.kpi-trend.negative {
  color: var(--ion-color-danger);
  background-color: rgba(var(--ion-color-danger-rgb), 0.1);
}

/* Grid de gráficos */
.charts-grid {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.chart-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

.chart-card {
  background: white;
  border-radius: 12px;
  padding: 16px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  height: 350px;
  display: flex;
  flex-direction: column;
}

.chart-card.full-width {
  grid-column: 1 / -1;
  height: 300px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.card-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: var(--ion-color-dark);
}

.time-selector {
  max-width: 300px;
}

.badge {
  padding: 4px 8px;
  border-radius: 16px;
  font-size: 12px;
  font-weight: 600;
}

.badge.success {
  background-color: rgba(var(--ion-color-success-rgb), 0.1);
  color: var(--ion-color-success);
}

.badge.warning {
  background-color: rgba(var(--ion-color-warning-rgb), 0.1);
  color: var(--ion-color-warning);
}

.badge.info {
  background-color: rgba(var(--ion-color-primary-rgb), 0.1);
  color: var(--ion-color-primary);
}

.badge.danger {
  background-color: rgba(var(--ion-color-danger-rgb), 0.1);
  color: var(--ion-color-danger);
}

/* Responsive */
@media (max-width: 768px) {
  .kpi-summary {
    grid-template-columns: 1fr;
  }
  .chart-card {
    height: 300px;
  }
}
</style>
