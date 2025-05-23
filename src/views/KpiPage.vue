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
            <span>KPIs</span>
          </div>
        </ion-title>
        <ion-buttons slot="end">
          <ion-button>
            <ion-icon slot="icon-only" :icon="notificationsOutline" color="light" />
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding" scroll-y="false">
      <!-- Resumen de KPIs -->
      <div class="kpi-summary">
        <div class="kpi-summary-card business">
          <div class="kpi-summary-icon">
            <ion-icon :icon="rocketOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">KPIs de Negocio</div>
            <div class="kpi-summary-value">4/5 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill" :style="{ width: '65%' }"></div>
            </div>
            <div class="progress-text">65% completado</div>
          </div>
        </div>
        
        <div class="kpi-summary-card technical">
          <div class="kpi-summary-icon">
            <ion-icon :icon="codeSlashOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">KPIs Técnicos</div>
            <div class="kpi-summary-value">5/5 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill" :style="{ width: '42%' }"></div>
            </div>
            <div class="progress-text">42% completado</div>
          </div>
        </div>
        
        <div class="kpi-summary-card overall">
          <div class="kpi-summary-icon">
            <ion-icon :icon="trendingUpOutline" />
          </div>
          <div class="kpi-summary-content">
            <div class="kpi-summary-title">Progreso General</div>
            <div class="kpi-summary-value">9/10 activos</div>
          </div>
          <div class="kpi-summary-progress">
            <div class="progress-bar">
              <div class="progress-fill" :style="{ width: '53%' }"></div>
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
    kpiTitle: 'Activación de usuario',
    title: 'Mejorar tasa de activación',
    description: 'Incrementar el porcentaje de nuevos usuarios que completan el onboarding.',
    progress: 30,
    deadline: '15 Jun 2025',
    owner: 'María Pérez',
    smart: [
      { letter: 'S', title: 'Específico', content: 'Usuarios que completan el onboarding' },
      { letter: 'M', title: 'Medible', content: 'De 40% a 60%' },
      { letter: 'A', title: 'Alcanzable', content: 'Añadiendo tutoriales guiados y tips in-app' },
      { letter: 'R', title: 'Relevante', content: 'Mejorar engagement y retención a largo plazo' },
      { letter: 'T', title: 'Temporal', content: 'En 45 días (15/06/2025)' }
    ]
  },
  {
    id: 4,
    kpiTitle: 'Retención',
    title: 'Aumentar retención de usuarios',
    description: 'Reducir churn mensual de la plataforma.',
    progress: 0,
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
  if (progress <= 0 ) return 'No iniciado';
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
/* Estilos base */
:root {
  --color-primary: #2563eb;
  --color-secondary: #059669;
  --color-accent: #7c3aed;
  --color-success: #059669;
  --color-warning: #d97706;
  --color-danger: #dc2626;
}

/* Estilos generales */
ion-content {
  --background: #0f172a;
  color: #e2e8f0;
}

.custom-toolbar {
  --background: #1e293b;
  --color: #f8fafc;
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
  border-radius: 16px;
  padding: 20px;
  display: grid;
  grid-template-columns: auto 1fr;
  grid-template-rows: auto auto;
  column-gap: 16px;
  row-gap: 16px;
  align-items: center;
  position: relative;
  overflow: hidden;
}

.kpi-summary-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
  z-index: -1;
}

.kpi-summary-card.business {
  background: linear-gradient(135deg, #1e3a8a30, #3b82f620);
  border: 1px solid #3b82f630;
}

.kpi-summary-card.technical {
  background: linear-gradient(135deg, #06522530, #10b98120);
  border: 1px solid #10b98130;
}

.kpi-summary-card.overall {
  background: linear-gradient(135deg, #5b21b630, #8b5cf620);
  border: 1px solid #8b5cf630;
}

.kpi-summary-icon {
  width: 50px;
  height: 50px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.kpi-summary-icon ion-icon {
  font-size: 24px;
  color: #fff;
}

.business .kpi-summary-icon {
  background: #3b82f6;
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.4);
}

.technical .kpi-summary-icon {
  background: #10b981;
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.4);
}

.overall .kpi-summary-icon {
  background: #8b5cf6;
  box-shadow: 0 0 20px rgba(139, 92, 246, 0.4);
}

.kpi-summary-content {
  grid-column: 2;
  grid-row: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.kpi-summary-title {
  font-size: 16px;
  font-weight: 600;
  color: #f1f5f9;
  margin-bottom: 4px;
}

.kpi-summary-value {
  font-size: 14px;
  font-weight: 500;
  color: #94a3b8;
  background: rgba(255, 255, 255, 0.1);
  padding: 4px 10px;
  border-radius: 12px;
}

.kpi-summary-progress {
  grid-column: 1 / -1;
  grid-row: 2;
}

.progress-bar {
  height: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 6px;
}

.progress-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 0.5s ease-in-out;
}

.business .progress-fill {
  background: #3b82f6;
  box-shadow: 0 0 10px rgba(59, 130, 246, 0.6);
}

.technical .progress-fill {
  background: #10b981;
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.6);
}

.overall .progress-fill {
  background: #8b5cf6;
  box-shadow: 0 0 10px rgba(139, 92, 246, 0.6);
}

.progress-text {
  font-size: 12px;
  font-weight: 500;
  color: #94a3b8;
  text-align: right;
}

/* Layout grid */
.page-container {
  max-width: 1200px;
  margin: 0 auto;
}

.blocks {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  height: calc(100% - 180px);
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
  padding-right: 8px;
  scrollbar-width: thin;
}

.block .kpi-cards::-webkit-scrollbar {
  width: 4px;
}

.block .kpi-cards::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 4px;
}

.block .kpi-cards::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
}

/* Sections */
.section-container {
  margin-bottom: 32px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.section-title {
  display: flex;
  align-items: center;
}

.section-icon {
  font-size: 20px;
  padding: 14px;
  border-radius: 14px;
  margin-right: 16px;
  color: #fff;
}

.section-icon.business {
  background: #3b82f6;
  box-shadow: 0 0 15px rgba(59, 130, 246, 0.4);
}

.section-icon.technical {
  background: #10b981;
  box-shadow: 0 0 15px rgba(16, 185, 129, 0.4);
}

.section-title h2 {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: #f1f5f9;
}

/* Accordion */
.custom-accordion {
  margin-bottom: 16px;
  border-radius: 14px;
  overflow: hidden;
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.accordion-header {
  --background: transparent;
  --border-color: transparent;
  --padding-start: 16px;
  --padding-end: 16px;
  --padding-top: 14px;
  --padding-bottom: 14px;
  --inner-padding-end: 0;
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
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: #3b82f6;
  color: #fff;
  font-weight: 600;
  margin-right: 14px;
  box-shadow: 0 0 15px rgba(59, 130, 246, 0.4);
}

.kpi-status {
  font-size: 12px;
  font-weight: 600;
  padding: 5px 10px;
  border-radius: 20px;
  white-space: nowrap;
}

.status-success {
  background: rgba(16, 185, 129, 0.2);
  color: #34d399;
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.status-warning {
  background: rgba(245, 158, 11, 0.2);
  color: #fbbf24;
  border: 1px solid rgba(245, 158, 11, 0.3);
}

.status-danger {
  background: rgba(239, 68, 68, 0.2);
  color: #f87171;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.accordion-content {
  background: rgba(255, 255, 255, 0.02);
  padding: 0;
}

.kpi-details {
  padding: 20px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

/* Inline header titles */
.kpi-header-inline {
  display: flex;
  flex-direction: column;
}

.kpi-header-inline .kpi-title {
  font-size: 16px;
  font-weight: 600;
  color: #f1f5f9;
  margin-bottom: 2px;
}

.kpi-header-inline .kpi-subtitle {
  font-size: 13px;
  color: #94a3b8;
}

/* KPI content */
.kpi-progress-container {
  margin-bottom: 20px;
  background: rgba(255, 255, 255, 0.05);
  padding: 16px;
  border-radius: 12px;
}

.kpi-progress-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.kpi-progress-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: #f1f5f9;
}

.kpi-progress-value {
  font-weight: 700;
  color: #3b82f6;
  font-size: 16px;
}

.kpi-progress-bar {
  height: 10px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 5px;
  overflow: hidden;
}

.kpi-progress-fill {
  height: 100%;
  border-radius: 5px;
  transition: width 0.5s ease-in-out;
}

.progress-fill-success {
  background: #10b981;
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.6);
}

.progress-fill-warning {
  background: #f59e0b;
  box-shadow: 0 0 10px rgba(245, 158, 11, 0.6);
}

.progress-fill-danger {
  background: #ef4444;
  box-shadow: 0 0 10px rgba(239, 68, 68, 0.6);
}

.kpi-description {
  color: #cbd5e1;
  margin-bottom: 20px;
  font-size: 14px;
  line-height: 1.6;
}

.kpi-meta {
  display: flex;
  gap: 20px;
  margin-bottom: 20px;
  background: rgba(255, 255, 255, 0.05);
  padding: 12px 16px;
  border-radius: 12px;
}

.kpi-meta-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #cbd5e1;
  font-weight: 500;
}

.kpi-meta-item ion-icon {
  font-size: 18px;
  color: #94a3b8;
}

/* SMART list */
.smart-container {
  background: rgba(255, 255, 255, 0.03);
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.smart-container h3 {
  margin: 0 0 16px;
  font-size: 16px;
  font-weight: 700;
  color: #f1f5f9;
  display: flex;
  align-items: center;
}

.smart-container h3::before {
  content: "";
  display: block;
  width: 4px;
  height: 16px;
  background: #3b82f6;
  margin-right: 10px;
  border-radius: 2px;
}

.smart-list {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.smart-item {
  display: flex;
  align-items: flex-start;
  transition: transform 0.2s;
}

.smart-letter {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 36px;
  height: 36px;
  border-radius: 10px;
  color: #fff;
  font-weight: 700;
  margin-right: 14px;
}

.letter-s {
  background: #3b82f6;
  box-shadow: 0 0 15px rgba(59, 130, 246, 0.4);
}

.letter-m {
  background: #2563eb;
  box-shadow: 0 0 15px rgba(37, 99, 235, 0.4);
}

.letter-a {
  background: #10b981;
  box-shadow: 0 0 15px rgba(16, 185, 129, 0.4);
}

.letter-r {
  background: #059669;
  box-shadow: 0 0 15px rgba(5, 150, 105, 0.4);
}

.letter-t {
  background: #8b5cf6;
  box-shadow: 0 0 15px rgba(139, 92, 246, 0.4);
}

.smart-content {
  flex: 1;
}

.smart-title {
  font-size: 14px;
  font-weight: 600;
  color: #f1f5f9;
  margin-bottom: 4px;
}

.smart-description {
  font-size: 13px;
  color: #94a3b8;
  line-height: 1.5;
}

/* Responsive */
@media (max-width: 768px) {
  .blocks {
    grid-template-columns: 1fr;
  }
  
  .kpi-meta {
    flex-direction: column;
    gap: 10px;
  }
  
  .kpi-summary {
    grid-template-columns: 1fr;
  }
}

/* Accordion expanded state */
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(255, 255, 255, 0.05);
}

ion-accordion.accordion-expanded .kpi-number {
  background: #2563eb;
}
</style>