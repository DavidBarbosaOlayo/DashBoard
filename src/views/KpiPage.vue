<template>
  <ion-page>
    <ion-header class="ion-no-border">
      <ion-toolbar class="custom-toolbar">
        <ion-buttons slot="start">
          <ion-menu-button color="light" />
        </ion-buttons>
        <ion-title class="ion-text-center">
          <div class="header-title">
            <ion-icon :icon="statsChartOutline" class="header-icon" />
            <span>Dashboard de KPIs</span>
          </div>
        </ion-title>
        <ion-buttons slot="end">
          <ion-button>
            <ion-icon slot="icon-only" :icon="notificationsOutline" color="light" />
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <!-- Resumen de KPIs -->
      <div class="kpi-summary">
        <div class="kpi-summary-card">
          <div class="kpi-summary-icon business">
            <ion-icon :icon="rocketOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">KPIs de Negocio</div>
            <div class="kpi-summary-value">2/2 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill business-fill" :style="{ width: '65%' }"></div>
            </div>
            <div class="progress-text">65% completado</div>
          </div>
        </div>
        
        <div class="kpi-summary-card">
          <div class="kpi-summary-icon technical">
            <ion-icon :icon="codeSlashOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">KPIs Técnicos</div>
            <div class="kpi-summary-value">2/2 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill technical-fill" :style="{ width: '42%' }"></div>
            </div>
            <div class="progress-text">42% completado</div>
          </div>
        </div>
        
        <div class="kpi-summary-card">
          <div class="kpi-summary-icon overall">
            <ion-icon :icon="trendingUpOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">Progreso General</div>
            <div class="kpi-summary-value">4/4 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill overall-fill" :style="{ width: '53%' }"></div>
            </div>
            <div class="progress-text">53% completado</div>
          </div>
        </div>
      </div>

      <div class="page-container">
        <!-- Filtros y Controles -->
        <div class="controls-container">
          <div class="filter-controls">
            <ion-segment v-model="activeFilter" mode="ios" class="custom-segment">
              <ion-segment-button value="all" class="segment-button">
                <ion-label>Todos</ion-label>
              </ion-segment-button>
              <ion-segment-button value="business" class="segment-button">
                <ion-label>Negocio</ion-label>
              </ion-segment-button>
              <ion-segment-button value="technical" class="segment-button">
                <ion-label>Técnicos</ion-label>
              </ion-segment-button>
            </ion-segment>
          </div>
          <!-- Eliminados los botones de expandir/colapsar que daban error -->
        </div>

        <!-- Sección de KPIs de Negocio -->
        <div class="section-container" v-show="activeFilter === 'all' || activeFilter === 'business'">
          <div class="section-header">
            <div class="section-title">
              <ion-icon :icon="rocketOutline" class="section-icon business" />
              <h2>KPIs de Negocio</h2>
            </div>
            <ion-button fill="clear" size="small" class="add-kpi-btn">
              <ion-icon slot="icon-only" :icon="addOutline" />
            </ion-button>
          </div>
          <div class="kpi-cards">
            <ion-accordion-group expand="inset" :multiple="true">
              <ion-accordion
                v-for="item in businessGoals"
                :key="item.id"
                :value="item.id.toString()"
                class="custom-accordion"
              >
                <ion-item slot="header" class="accordion-header">
                  <ion-label>
                    <div class="accordion-title">
                      <span class="kpi-number">{{ item.id }}</span>
                      <span>{{ item.kpiTitle }}</span>
                    </div>
                  </ion-label>
                  <div class="kpi-status" :class="getStatusClass(item.progress)">
                    {{ getStatusText(item.progress) }}
                  </div>
                </ion-item>
                <div class="accordion-content" slot="content">
                  <div class="kpi-details">
                    <div class="kpi-header">
                      <h2 class="kpi-title">{{ item.title }}</h2>
                    </div>
                    
                    <div class="kpi-progress-container">
                      <div class="kpi-progress-header">
                        <h3>Progreso</h3>
                        <span class="kpi-progress-value">{{ item.progress }}%</span>
                      </div>
                      <div class="kpi-progress-bar">
                        <div class="kpi-progress-fill" :class="getProgressFillClass(item.progress)" :style="{ width: `${item.progress}%` }"></div>
                      </div>
                    </div>
                    
                    <p class="kpi-description">{{ item.description }}</p>
                    
                    <div class="kpi-meta">
                      <div class="kpi-meta-item">
                        <ion-icon :icon="calendarOutline" />
                        <span>{{ item.deadline }}</span>
                      </div>
                      <div class="kpi-meta-item">
                        <ion-icon :icon="personOutline" />
                        <span>{{ item.owner }}</span>
                      </div>
                    </div>
                    
                    <div class="smart-container">
                      <h3>Objetivos SMART</h3>
                      <div class="smart-list">
                        <div
                          v-for="(element, index) in item.smart"
                          :key="index"
                          class="smart-item"
                        >
                          <div
                            class="smart-letter"
                            :class="`letter-${element.letter.toLowerCase()}`"
                          >
                            {{ element.letter }}
                          </div>
                          <div class="smart-content">
                            <div class="smart-title">{{ element.title }}</div>
                            <div class="smart-description">{{ element.content }}</div>
                          </div>
                        </div>
                      </div>
                    </div>
                    
                    <!-- Eliminados los botones de editar y detalles que daban error -->
                  </div>
                </div>
              </ion-accordion>
            </ion-accordion-group>
          </div>
        </div>

        <!-- Sección de KPIs Técnicos -->
        <div class="section-container" v-show="activeFilter === 'all' || activeFilter === 'technical'">
          <div class="section-header">
            <div class="section-title">
              <ion-icon :icon="codeSlashOutline" class="section-icon technical" />
              <h2>KPIs Técnicos</h2>
            </div>
            <ion-button fill="clear" size="small" class="add-kpi-btn">
              <ion-icon slot="icon-only" :icon="addOutline" />
            </ion-button>
          </div>
          <div class="kpi-cards">
            <ion-accordion-group expand="inset" :multiple="true">
              <ion-accordion
                v-for="item in technicalGoals"
                :key="item.id"
                :value="item.id.toString()"
                class="custom-accordion"
              >
                <ion-item slot="header" class="accordion-header">
                  <ion-label>
                    <div class="accordion-title">
                      <span class="kpi-number">{{ item.id }}</span>
                      <span>{{ item.kpiTitle }}</span>
                    </div>
                  </ion-label>
                  <div class="kpi-status" :class="getStatusClass(item.progress)">
                    {{ getStatusText(item.progress) }}
                  </div>
                </ion-item>
                <div class="accordion-content" slot="content">
                  <div class="kpi-details">
                    <div class="kpi-header">
                      <h2 class="kpi-title">{{ item.title }}</h2>
                    </div>
                    
                    <div class="kpi-progress-container">
                      <div class="kpi-progress-header">
                        <h3>Progreso</h3>
                        <span class="kpi-progress-value">{{ item.progress }}%</span>
                      </div>
                      <div class="kpi-progress-bar">
                        <div class="kpi-progress-fill" :class="getProgressFillClass(item.progress)" :style="{ width: `${item.progress}%` }"></div>
                      </div>
                    </div>
                    
                    <p class="kpi-description">{{ item.description }}</p>
                    
                    <div class="kpi-meta">
                      <div class="kpi-meta-item">
                        <ion-icon :icon="calendarOutline" />
                        <span>{{ item.deadline }}</span>
                      </div>
                      <div class="kpi-meta-item">
                        <ion-icon :icon="personOutline" />
                        <span>{{ item.owner }}</span>
                      </div>
                    </div>
                    
                    <div class="smart-container">
                      <h3>Objetivos SMART</h3>
                      <div class="smart-list">
                        <div
                          v-for="(element, index) in item.smart"
                          :key="index"
                          class="smart-item"
                        >
                          <div
                            class="smart-letter"
                            :class="`letter-${element.letter.toLowerCase()}`"
                          >
                            {{ element.letter }}
                          </div>
                          <div class="smart-content">
                            <div class="smart-title">{{ element.title }}</div>
                            <div class="smart-description">{{ element.content }}</div>
                          </div>
                        </div>
                      </div>
                    </div>
                    
                    <!-- Eliminados los botones de editar y detalles que daban error -->
                  </div>
                </div>
              </ion-accordion>
            </ion-accordion-group>
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonButtons,
  IonContent,
  IonHeader,
  IonMenuButton,
  IonPage,
  IonTitle,
  IonToolbar,
  IonAccordionGroup,
  IonAccordion,
  IonItem,
  IonLabel,
  IonIcon,
  IonButton,
  IonSegment,
  IonSegmentButton
} from '@ionic/vue';
import { ref } from 'vue';
import {
  statsChartOutline,
  rocketOutline,
  codeSlashOutline,
  notificationsOutline,
  trendingUpOutline,
  calendarOutline,
  personOutline,
  addOutline
} from 'ionicons/icons';

interface SmartElement {
  letter: string;
  title: string;
  content: string;
}

interface SmartGoal {
  id: number;
  kpiTitle: string; // Título corto para el encabezado del acordeón
  title: string; // Título completo para el contenido
  description: string;
  progress: number;
  deadline: string;
  owner: string;
  smart: SmartElement[];
}

const activeFilter = ref('all');

const businessGoals = ref<SmartGoal[]>([
  {
    id: 1,
    kpiTitle: 'Visitas Web', // Título corto para el encabezado
    title: 'Más Visitas: Aumentar tráfico web', // Título completo
    description: 'Aumentar las visitas un 50% en 30 días duplicando contenido y mejorando SEO.',
    progress: 65,
    deadline: '30 Jun 2025',
    owner: 'Ana Martínez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar visitas en un 50%' },
      { letter: 'M', title: 'Medible', content: 'De 1.000 a 1.500 visitas diarias' },
      { letter: 'A', title: 'Alcanzable', content: 'Duplicando contenido y optimizando SEO' },
      { letter: 'R', title: 'Relevante', content: 'Preparar lanzamiento de nueva funcionalidad' },
      { letter: 'T', title: 'Temporal', content: 'En 30 días (30 de junio)' }
    ]
  },
  {
    id: 2,
    kpiTitle: 'Ventas Anuales', // Título corto para el encabezado
    title: 'Más Ventas: Incrementar ingresos', // Título completo
    description: 'Subir un 20% las ventas en 1 año ofreciendo nuevos productos y mejorando la conversión.',
    progress: 35,
    deadline: '31 Dic 2025',
    owner: 'Carlos Rodríguez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar ventas en un 20%' },
      { letter: 'M', title: 'Medible', content: 'De 200.000€ a 240.000€ anuales' },
      { letter: 'A', title: 'Alcanzable', content: 'Con nuevos productos y mejoras en conversión' },
      { letter: 'R', title: 'Relevante', content: 'Aprovechar base de clientes existente' },
      { letter: 'T', title: 'Temporal', content: 'En 12 meses (31 de diciembre)' }
    ]
  }
]);

const technicalGoals = ref<SmartGoal[]>([
  {
    id: 1,
    kpiTitle: 'Tiempo de Carga', // Título corto para el encabezado
    title: 'Rendimiento: Optimizar velocidad de carga', // Título completo
    description: 'Reducir tiempo de carga de la app en un 30% antes de fin de mes mejorando imágenes y caché.',
    progress: 42,
    deadline: '31 May 2025',
    owner: 'David López',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Reducir tiempo de carga de la aplicación' },
      { letter: 'M', title: 'Medible', content: 'Un 30% menos (de 3s a 2.1s)' },
      { letter: 'A', title: 'Alcanzable', content: 'Mejorando imágenes y sistema de caché' },
      { letter: 'R', title: 'Relevante', content: 'Mejor experiencia de usuario y retención' },
      { letter: 'T', title: 'Temporal', content: 'Antes de fin de mes (31 de mayo)' }
    ]
  },
  {
    id: 2,
    kpiTitle: 'Cobertura Tests', // Título corto para el encabezado
    title: 'Calidad: Mejorar cobertura de tests', // Título completo
    description: 'Cubrir 90% del código con tests antes del próximo sprint para aumentar la fiabilidad.',
    progress: 28,
    deadline: '15 Jun 2025',
    owner: 'Elena Sánchez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar cobertura de tests' },
      { letter: 'M', title: 'Medible', content: '90% del código base' },
      { letter: 'A', title: 'Alcanzable', content: 'Escribiendo tests unitarios e integración' },
      { letter: 'R', title: 'Relevante', content: 'Aumentar fiabilidad y reducir bugs' },
      { letter: 'T', title: 'Temporal', content: 'Antes del próximo sprint (15 de junio)' }
    ]
  }
]);

// Funciones para determinar el estado del KPI
const getStatusClass = (progress: number) => {
  if (progress < 30) return 'status-danger';
  if (progress < 70) return 'status-warning';
  return 'status-success';
};

const getStatusText = (progress: number) => {
  if (progress < 30) return 'En riesgo';
  if (progress < 70) return 'En progreso';
  return 'En objetivo';
};

// Función para determinar la clase de relleno de la barra de progreso
const getProgressFillClass = (progress: number) => {
  if (progress < 30) return 'progress-fill-danger';
  if (progress < 70) return 'progress-fill-warning';
  return 'progress-fill-success';
};
</script>

<style scoped>
/* Variables de colores con mejor contraste */
:root {
  --color-primary: #2563eb;
  --color-primary-light: #3b82f6;
  --color-primary-dark: #1d4ed8;
  
  --color-secondary: #059669;
  --color-secondary-light: #10b981;
  --color-secondary-dark: #047857;
  
  --color-accent: #7c3aed;
  --color-accent-light: #8b5cf6;
  --color-accent-dark: #6d28d9;
  
  --color-success: #059669;
  --color-warning: #d97706;
  --color-danger: #dc2626;
  
  --color-dark: #111827;
  --color-gray-50: #f9fafb;
  --color-gray-100: #f3f4f6;
  --color-gray-200: #e5e7eb;
  --color-gray-300: #d1d5db;
  --color-gray-400: #9ca3af;
  --color-gray-500: #6b7280;
  --color-gray-600: #4b5563;
  --color-gray-700: #374151;
  --color-gray-800: #1f2937;
  --color-gray-900: #111827;
}

/* Header */
.custom-toolbar {
  --background: #1e293b;
  --color: #ffffff;
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

/* KPI Summary Cards */
.kpi-summary {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 16px;
  margin-bottom: 24px;
}

.kpi-summary-card {
  background-color: #ffffff;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.kpi-summary-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.kpi-summary-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 12px;
}

.kpi-summary-icon ion-icon {
  font-size: 24px;
  color: white;
}

.kpi-summary-icon.business {
  background: linear-gradient(135deg, #3b82f6, #1d4ed8);
}

.kpi-summary-icon.technical {
  background: linear-gradient(135deg, #10b981, #047857);
}

.kpi-summary-icon.overall {
  background: linear-gradient(135deg, #8b5cf6, #6d28d9);
}

.kpi-summary-content {
  margin-bottom: 12px;
}

.kpi-summary-title {
  font-size: 16px;
  font-weight: 600;
  color: #1e293b;
  margin-bottom: 4px;
}

.kpi-summary-value {
  font-size: 14px;
  color: #475569;
}

.kpi-summary-progress {
  margin-top: auto;
}

.progress-bar {
  height: 8px;
  background-color: #e2e8f0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 4px;
}

.progress-fill {
  height: 100%;
  border-radius: 4px;
}

.business-fill {
  background: linear-gradient(90deg, #3b82f6, #1d4ed8);
}

.technical-fill {
  background: linear-gradient(90deg, #10b981, #047857);
}

.overall-fill {
  background: linear-gradient(90deg, #8b5cf6, #6d28d9);
}

.progress-fill-success {
  background: linear-gradient(90deg, #10b981, #047857);
}

.progress-fill-warning {
  background: linear-gradient(90deg, #f59e0b, #d97706);
}

.progress-fill-danger {
  background: linear-gradient(90deg, #ef4444, #dc2626);
}

.progress-text {
  font-size: 12px;
  color: #475569;
  text-align: right;
}

/* Layout */
.page-container {
  max-width: 1200px;
  margin: 0 auto;
}

.controls-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  flex-wrap: wrap;
  gap: 16px;
}

.filter-controls {
  flex: 1;
  min-width: 280px;
}

.action-controls {
  display: flex;
  gap: 8px;
}

.section-container {
  margin-bottom: 32px;
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.section-title {
  display: flex;
  align-items: center;
}

.section-icon {
  font-size: 20px;
  padding: 12px;
  border-radius: 12px;
  margin-right: 16px;
  color: white;
}

.section-icon.business {
  background: linear-gradient(135deg, #3b82f6, #1d4ed8);
}

.section-icon.technical {
  background: linear-gradient(135deg, #10b981, #047857);
}

.section-title h2 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
  color: #1e293b;
}

.add-kpi-btn {
  --color: #475569;
}

/* Accordion */
.custom-accordion {
  margin-bottom: 16px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.2s;
}

.custom-accordion:hover {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.accordion-header {
  --background: white;
  --border-color: transparent;
  --padding-start: 16px;
  --padding-end: 16px;
  --padding-top: 12px;
  --padding-bottom: 12px;
}

.accordion-title {
  display: flex;
  align-items: center;
  font-weight: 500;
}

.kpi-number {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: #2563eb;
  color: white;
  font-weight: 600;
  margin-right: 12px;
}

.kpi-status {
  font-size: 12px;
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 16px;
}

.status-success {
  background-color: rgba(16, 185, 129, 0.15);
  color: #047857;
}

.status-warning {
  background-color: rgba(245, 158, 11, 0.15);
  color: #b45309;
}

.status-danger {
  background-color: rgba(239, 68, 68, 0.15);
  color: #b91c1c;
}

.accordion-content {
  background-color: white;
  padding: 0;
}

.kpi-details {
  padding: 16px;
}

/* Título del KPI */
.kpi-header {
  margin-bottom: 16px;
}

.kpi-title {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: #1e293b;
  border-bottom: 2px solid #e2e8f0;
  padding-bottom: 8px;
}

.kpi-progress-container {
  margin-bottom: 16px;
}

.kpi-progress-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.kpi-progress-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: #1e293b;
}

.kpi-progress-value {
  font-weight: 600;
  color: #2563eb;
}

.kpi-progress-bar {
  height: 8px;
  background-color: #e2e8f0;
  border-radius: 4px;
  overflow: hidden;
}

.kpi-progress-fill {
  height: 100%;
  border-radius: 4px;
}

.kpi-description {
  color: #334155;
  margin-bottom: 16px;
  font-size: 14px;
  line-height: 1.5;
}

.kpi-meta {
  display: flex;
  gap: 16px;
  margin-bottom: 16px;
}

.kpi-meta-item {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 13px;
  color: #475569;
}

.kpi-meta-item ion-icon {
  font-size: 16px;
}

.smart-container {
  background-color: #f8fafc;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
  border: 1px solid #e2e8f0;
}

.smart-container h3 {
  margin: 0 0 12px 0;
  font-size: 16px;
  font-weight: 600;
  color: #1e293b;
}

.smart-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.smart-item {
  display: flex;
  align-items: flex-start;
}

.smart-letter {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 32px;
  height: 32px;
  border-radius: 8px;
  color: white;
  font-weight: 600;
  margin-right: 12px;
}

.letter-s { background-color: #2563eb; }
.letter-m { background-color: #1d4ed8; }
.letter-a { background-color: #059669; }
.letter-r { background-color: #047857; }
.letter-t { background-color: #7c3aed; }

.smart-content {
  flex: 1;
}

.smart-title {
  font-size: 14px;
  font-weight: 600;
  color: #1e293b;
  margin-bottom: 2px;
}

.smart-description {
  font-size: 13px;
  color: #334155;
}

.kpi-actions {
  display: flex;
  gap: 8px;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .controls-container {
    flex-direction: column;
    align-items: stretch;
  }
  
  .action-controls {
    justify-content: flex-end;
  }
  
  .kpi-meta {
    flex-direction: column;
    gap: 8px;
  }
}

/* Accordion expanded state */
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: #f8fafc;
}

ion-accordion.accordion-expanded .kpi-number {
  background-color: #1d4ed8;
}

/* Segment styling - CORREGIDO para mostrar el texto seleccionado */
.custom-segment {
  --background: white;
}

ion-segment-button {
  --color: #475569;
  --color-checked: #2563eb;
  --indicator-color: #2563eb;
  --background-checked: rgba(37, 99, 235, 0.1);
  font-weight: 500;
}

/* Asegurar que el texto sea visible cuando está seleccionado */
.segment-button {
  opacity: 1 !important;
}

ion-segment-button.segment-button-checked {
  color: #2563eb !important;
  --color: #2563eb !important;
  opacity: 1 !important;
}

ion-segment-button.segment-button-checked ion-label {
  opacity: 1 !important;
  color: #2563eb !important;
}

/* Botones con mejor contraste */
ion-button {
  --color: #2563eb;
}

ion-button[fill="outline"] {
  --border-color: #2563eb;
  --color: #2563eb;
}

/* Fondo general */
ion-content {
  --background: #f1f5f9;
}

ion-segment-button.segment-button-checked {
    z-index: 0 !important;
  }

  /* 2) (Opcional) Si quieres afinar con shadow-parts y asegurarte
        de que el fondo del indicador esté por detrás */
  ion-segment-button::part(indicator-background) {
    z-index: 0;
  }
  ion-segment-button::part(native) {
    position: relative;
    z-index: 1;
  }
</style>