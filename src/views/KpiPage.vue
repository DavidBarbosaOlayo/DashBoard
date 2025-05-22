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

    <!-- scroll-y="false" desactiva el scroll global -->
    <ion-content class="ion-padding" scroll-y="false">
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

      <!-- Contenedor en Grid de dos columnas -->
      <div class="blocks page-container">
        <!-- Sección de KPIs de Negocio -->
        <div class="section-container block">
          <div class="section-header">
            <div class="section-title">
              <ion-icon :icon="rocketOutline" class="section-icon business" />
              <h2>KPIs de Negocio</h2>
            </div>
          </div>
          <div class="kpi-cards">
            <ion-accordion-group expand="inset" :multiple="true">
              <ion-accordion
                v-for="item in businessGoals"
                :key="item.id"
                :value="item.id.toString()"
                class="custom-accordion"
              >
                <!-- Header con título y subtítulo -->
                <ion-item slot="header" class="accordion-header">
                  <ion-label>
                    <div class="accordion-title">
                      <span class="kpi-number">{{ item.id }}</span>
                      <div class="kpi-header-inline">
                        <div class="kpi-title">{{ item.title }}</div>
                        <div class="kpi-subtitle">{{ item.kpiTitle }}</div>
                      </div>
                    </div>
                  </ion-label>
                  <div class="kpi-status" :class="getStatusClass(item.progress)">
                    {{ getStatusText(item.progress) }}
                  </div>
                </ion-item>
                
                <!-- Contenido al desplegar -->
                <div class="accordion-content" slot="content">
                  <div class="kpi-details">
                    <div class="kpi-progress-container">
                      <div class="kpi-progress-header">
                        <h3>Progreso</h3>
                        <span class="kpi-progress-value">{{ item.progress }}%</span>
                      </div>
                      <div class="kpi-progress-bar">
                        <div
                          class="kpi-progress-fill"
                          :class="getProgressFillClass(item.progress)"
                          :style="{ width: `${item.progress}%` }"
                        ></div>
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
                  </div>
                </div>
              </ion-accordion>
            </ion-accordion-group>
          </div>
        </div>

        <!-- Sección de KPIs Técnicos -->
        <div class="section-container block">
          <div class="section-header">
            <div class="section-title">
              <ion-icon :icon="codeSlashOutline" class="section-icon technical" />
              <h2>KPIs Técnicos</h2>
            </div>
          </div>
          <div class="kpi-cards">
            <ion-accordion-group expand="inset" :multiple="true">
              <ion-accordion
                v-for="item in technicalGoals"
                :key="item.id"
                :value="item.id.toString()"
                class="custom-accordion"
              >
                <!-- Header con título y subtítulo -->
                <ion-item slot="header" class="accordion-header">
                  <ion-label>
                    <div class="accordion-title">
                      <span class="kpi-number">{{ item.id }}</span>
                      <div class="kpi-header-inline">
                        <div class="kpi-title">{{ item.title }}</div>
                        <div class="kpi-subtitle">{{ item.kpiTitle }}</div>
                      </div>
                    </div>
                  </ion-label>
                  <div class="kpi-status" :class="getStatusClass(item.progress)">
                    {{ getStatusText(item.progress) }}
                  </div>
                </ion-item>
                
                <!-- Contenido al desplegar -->
                <div class="accordion-content" slot="content">
                  <div class="kpi-details">
                    <div class="kpi-progress-container">
                      <div class="kpi-progress-header">
                        <h3>Progreso</h3>
                        <span class="kpi-progress-value">{{ item.progress }}%</span>
                      </div>
                      <div class="kpi-progress-bar">
                        <div
                          class="kpi-progress-fill"
                          :class="getProgressFillClass(item.progress)"
                          :style="{ width: `${item.progress}%` }"
                        ></div>
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
import { ref } from 'vue'
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
import {
  statsChartOutline,
  rocketOutline,
  codeSlashOutline,
  notificationsOutline,
  trendingUpOutline,
  calendarOutline,
  personOutline,
} from 'ionicons/icons';

interface SmartElement {
  letter: string;
  title: string;
  content: string;
}

interface SmartGoal {
  id: number;
  kpiTitle: string;
  title: string;
  description: string;
  progress: number;
  deadline: string;
  owner: string;
  smart: SmartElement[];
}

const activeFilter = ref<'all' | 'business' | 'technical'>('all');

const businessGoals = ref<SmartGoal[]>([
  {
    id: 1,
    kpiTitle: 'Visitas Web',
    title: 'Aumentar tráfico web',
    description: 'Incrementar el número de visitas diarias a la web.',
    progress: 60,
    deadline: '30 Jun 2025',
    owner: 'Ana Martínez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar visitas únicas diarias' },
      { letter: 'M', title: 'Medible', content: 'De 1 000 a 1 500 visitas' },
      { letter: 'A', title: 'Alcanzable', content: 'Mejorando SEO y contenido' },
      { letter: 'R', title: 'Relevante', content: 'Potenciar captación de leads' },
      { letter: 'T', title: 'Temporal', content: 'En 30 días (30/06/2025)' }
    ]
  },
  {
    id: 2,
    kpiTitle: 'Descargas',
    title: 'Incrementar descargas de la app',
    description: 'Lograr más instalaciones de la app móvil.',
    progress: 45,
    deadline: '31 Jul 2025',
    owner: 'Luis Gómez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar descargas semanales' },
      { letter: 'M', title: 'Medible', content: 'De 5 000 a 7 500 descargas' },
      { letter: 'A', title: 'Alcanzable', content: 'Campañas en redes y ASO' },
      { letter: 'R', title: 'Relevante', content: 'Expandir base de usuarios' },
      { letter: 'T', title: 'Temporal', content: 'En 60 días (31/07/2025)' }
    ]
  },
  {
    id: 3,
    kpiTitle: 'Conversión',
    title: 'Mejorar tasa de conversión',
    description: 'Aumentar porcentaje de visitantes que compran.',
    progress: 30,
    deadline: '15 Jun 2025',
    owner: 'María Pérez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Incrementar ratio de conversión' },
      { letter: 'M', title: 'Medible', content: 'De 2% a 3%' },
      { letter: 'A', title: 'Alcanzable', content: 'Optimizando checkout y CTA' },
      { letter: 'R', title: 'Relevante', content: 'Maximizar ingresos' },
      { letter: 'T', title: 'Temporal', content: 'En 45 días (15/06/2025)' }
    ]
  },
  {
    id: 4,
    kpiTitle: 'Retención',
    title: 'Aumentar retención de usuarios',
    description: 'Reducir churn mensual de la plataforma.',
    progress: 50,
    deadline: '31 Ago 2025',
    owner: 'Carlos Ruiz',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Reducir churn rate mensual' },
      { letter: 'M', title: 'Medible', content: 'De 10% a 7%' },
      { letter: 'A', title: 'Alcanzable', content: 'Mejorando onboarding y soporte' },
      { letter: 'R', title: 'Relevante', content: 'Fidelizar clientes' },
      { letter: 'T', title: 'Temporal', content: 'En 90 días (31/08/2025)' }
    ]
  },
  {
    id: 5,
    kpiTitle: 'Ingresos',
    title: 'Incrementar ingresos mensuales',
    description: 'Subir facturación mensual promedio.',
    progress: 40,
    deadline: '31 Dic 2025',
    owner: 'Laura Díaz',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar MRR' },
      { letter: 'M', title: 'Medible', content: 'De 20 000€ a 25 000€' },
      { letter: 'A', title: 'Alcanzable', content: 'Lanzando nuevos planes' },
      { letter: 'R', title: 'Relevante', content: 'Escalar negocio' },
      { letter: 'T', title: 'Temporal', content: 'En 12 meses (31/12/2025)' }
    ]
  }
]);

const technicalGoals = ref<SmartGoal[]>([
  {
    id: 1,
    kpiTitle: 'Carga App',
    title: 'Reducir tiempo de carga',
    description: 'Optimizar recursos para acelerar carga.',
    progress: 50,
    deadline: '31 May 2025',
    owner: 'David López',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Reducir loading time' },
      { letter: 'M', title: 'Medible', content: 'De 3s a 2s' },
      { letter: 'A', title: 'Alcanzable', content: 'Optimización de imágenes y cache' },
      { letter: 'R', title: 'Relevante', content: 'Mejor UX' },
      { letter: 'T', title: 'Temporal', content: 'Antes 31/05/2025' }
    ]
  },
  {
    id: 2,
    kpiTitle: 'Cobertura',
    title: 'Aumentar cobertura de tests',
    description: 'Extender tests unitarios e integración.',
    progress: 35,
    deadline: '15 Jun 2025',
    owner: 'Elena Sánchez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Incrementar test coverage' },
      { letter: 'M', title: 'Medible', content: 'De 70% a 90%' },
      { letter: 'A', title: 'Alcanzable', content: 'Escritura de tests con Vitest' },
      { letter: 'R', title: 'Relevante', content: 'Calidad de código' },
      { letter: 'T', title: 'Temporal', content: 'Antes 15/06/2025' }
    ]
  },
  {
    id: 3,
    kpiTitle: 'Errores',
    title: 'Reducir tasa de errores',
    description: 'Minimizar errores en producción.',
    progress: 20,
    deadline: '30 Jun 2025',
    owner: 'Jorge Fernández',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Reducir error rate' },
      { letter: 'M', title: 'Medible', content: 'De 5% a 2%' },
      { letter: 'A', title: 'Alcanzable', content: 'Implementar Sentry y QA' },
      { letter: 'R', title: 'Relevante', content: 'Estabilidad de la app' },
      { letter: 'T', title: 'Temporal', content: 'En 60 días (30/06/2025)' }
    ]
  },
  {
    id: 4,
    kpiTitle: 'Disponibilidad',
    title: 'Mejorar uptime',
    description: 'Mantener servicio online constantemente.',
    progress: 95,
    deadline: '31 Dic 2025',
    owner: 'Patricia Gómez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Aumentar uptime' },
      { letter: 'M', title: 'Medible', content: 'De 98% a 99.9%' },
      { letter: 'A', title: 'Alcanzable', content: 'Implementar monitoring y réplicas' },
      { letter: 'R', title: 'Relevante', content: 'Confiabilidad del sistema' },
      { letter: 'T', title: 'Temporal', content: 'En 12 meses (31/12/2025)' }
    ]
  },
  {
    id: 5,
    kpiTitle: 'Latencia',
    title: 'Reducir latencia API',
    description: 'Optimizar endpoints críticos.',
    progress: 30,
    deadline: '31 Jul 2025',
    owner: 'Miguel Torres',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Reducir respuesta API' },
      { letter: 'M', title: 'Medible', content: 'De 200 ms a 100 ms' },
      { letter: 'A', title: 'Alcanzable', content: 'Caching y optimización de consultas' },
      { letter: 'R', title: 'Relevante', content: 'Rapidez de la app' },
      { letter: 'T', title: 'Temporal', content: 'En 60 días (31/07/2025)' }
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
/* Variables de colores */
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
  background: #ffffff;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  transition: transform .2s, box-shadow .2s;
}
.kpi-summary-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
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
  color: #fff;
}
.kpi-summary-icon.business { background: linear-gradient(135deg,#3b82f6,#1d4ed8); }
.kpi-summary-icon.technical { background: linear-gradient(135deg,#10b981,#047857); }
.kpi-summary-icon.overall   { background: linear-gradient(135deg,#8b5cf6,#6d28d9); }
.kpi-summary-content { margin-bottom: 12px; }
.kpi-summary-title   { font-size: 16px; font-weight: 600; color: #1e293b; margin-bottom: 4px; }
.kpi-summary-value   { font-size: 14px; color: #475569; }
.kpi-summary-progress { margin-top: auto; }
.progress-bar {
  height: 8px;
  background: #e2e8f0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 4px;
}
.progress-fill { height: 100%; border-radius: 4px; }
.business-fill  { background: linear-gradient(90deg,#3b82f6,#1d4ed8); }
.technical-fill { background: linear-gradient(90deg,#10b981,#047857); }
.overall-fill   { background: linear-gradient(90deg,#8b5cf6,#6d28d9); }
.progress-fill-success { background: linear-gradient(90deg,#10b981,#047857); }
.progress-fill-warning { background: linear-gradient(90deg,#f59e0b,#d97706); }
.progress-fill-danger  { background: linear-gradient(90deg,#ef4444,#dc2626); }
.progress-text { font-size: 12px; color: #475569; text-align: right; }

/* Layout grid */
.page-container { max-width: 1200px; margin: 0 auto; }
.blocks {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
  height: 100%;
  overflow: hidden;
}
.block {
  display: flex;
  flex-direction: column;
  height: 100%;
  overflow: hidden;
}
.block .kpi-cards {
  flex: 1;
  overflow: auto;
  padding-right: 4px;
}

/* Sections */
.section-container {
  margin-bottom: 32px;
  animation: fadeIn .3s ease-in-out;
}
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
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
  color: #fff;
}
.section-icon.business  { background: linear-gradient(135deg,#3b82f6,#1d4ed8); }
.section-icon.technical { background: linear-gradient(135deg,#10b981,#047857); }
.section-title h2 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
  color: #1e293b;
}

/* Accordion */
.custom-accordion {
  margin-bottom: 16px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  transition: box-shadow .2s;
}
.custom-accordion:hover {
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}
.accordion-header {
  --background: #fff;
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
  background: #2563eb;
  color: #fff;
  font-weight: 600;
  margin-right: 12px;
}
.kpi-status {
  font-size: 12px;
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 16px;
}
.status-success { background: rgba(16,185,129,0.15); color: #047857; }
.status-warning { background: rgba(245,158,11,0.15); color: #b45309; }
.status-danger  { background: rgba(239,68,68,0.15); color: #b91c1c; }
.accordion-content { background: #fff; padding: 0; }
.kpi-details { padding: 16px; }

/* Inline header titles */
.kpi-header-inline {
  display: flex;
  flex-direction: column;
  margin-left: 12px;
}
.kpi-header-inline .kpi-title {
  font-size: 16px;
  font-weight: 600;
  color: #1e293b;
}
.kpi-header-inline .kpi-subtitle {
  font-size: 12px;
  color: #475569;
}

/* KPI content */
.kpi-progress-container { margin-bottom: 16px; }
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
.kpi-progress-value { font-weight: 600; color: #2563eb; }
.kpi-progress-bar {
  height: 8px;
  background: #e2e8f0;
  border-radius: 4px;
  overflow: hidden;
}
.kpi-progress-fill { height: 100%; border-radius: 4px; }

.kpi-description { color: #334155; margin-bottom: 16px; font-size: 14px; line-height: 1.5; }
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
.kpi-meta-item ion-icon { font-size: 16px; }

/* SMART list */
.smart-container {
  background: #f8fafc;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
  border: 1px solid #e2e8f0;
}
.smart-container h3 {
  margin: 0 0 12px;
  font-size: 16px;
  font-weight: 600;
  color: #1e293b;
}
.smart-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.smart-item { display: flex; align-items: flex-start; }
.smart-letter {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 32px;
  height: 32px;
  border-radius: 8px;
  color: #fff;
  font-weight: 600;
  margin-right: 12px;
}
.letter-s { background: #2563eb; }
.letter-m { background: #1d4ed8; }
.letter-a { background: #059669; }
.letter-r { background: #047857; }
.letter-t { background: #7c3aed; }
.smart-content { flex: 1; }
.smart-title {
  font-size: 14px;
  font-weight: 600;
  color: #1e293b;
  margin-bottom: 2px;
}
.smart-description { font-size: 13px; color: #334155; }

/* Responsive: sólo kpi-meta */
@media (max-width: 768px) {
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

/* Button contrast & background */
ion-button {
  --color: #2563eb;
}
ion-button[fill="outline"] {
  --border-color: #2563eb;
  --color: #2563eb;
}
ion-content {
  --background: #f1f5f9;
}
</style>
