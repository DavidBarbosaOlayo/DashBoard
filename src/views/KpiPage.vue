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

    <ion-content class="ion-padding">
      <div class="page-container">
        <!-- Sección de KPIs de Negocio -->
        <div class="section-container">
          <div class="section-header">
            <ion-icon :icon="rocketOutline" class="section-icon business" />
            <h2>KPIs de Negocio</h2>
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
                      <span>{{ item.title }}</span>
                    </div>
                  </ion-label>
                </ion-item>
                <div class="ion-padding accordion-content" slot="content">
                  <p class="kpi-description">{{ item.description }}</p>
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
                      <div class="smart-content">{{ element.content }}</div>
                    </div>
                  </div>
                </div>
              </ion-accordion>
            </ion-accordion-group>
          </div>
        </div>

        <!-- Sección de KPIs Técnicos -->
        <div class="section-container">
          <div class="section-header">
            <ion-icon :icon="codeSlashOutline" class="section-icon technical" />
            <h2>KPIs Técnicos</h2>
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
                      <span>{{ item.title }}</span>
                    </div>
                  </ion-label>
                </ion-item>
                <div class="ion-padding accordion-content" slot="content">
                  <p class="kpi-description">{{ item.description }}</p>
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
                      <div class="smart-content">{{ element.content }}</div>
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
  IonButton
} from '@ionic/vue';
import { ref } from 'vue';
import {
  statsChartOutline,
  rocketOutline,
  codeSlashOutline,
  notificationsOutline
} from 'ionicons/icons';

interface SmartElement {
  letter: string;
  content: string;
}

interface SmartGoal {
  id: number;
  title: string;
  description: string;
  smart: SmartElement[];
}

const businessGoals = ref<SmartGoal[]>([
  {
    id: 1,
    title: 'Aumentar visitas',
    description: 'Aumentar las visitas un 50% en 30 días duplicando contenido.',
    smart: [
      { letter: 'S', content: 'Aumentar visitas en un 50%' },
      { letter: 'M', content: 'De 1.000 a 1.500' },
      { letter: 'A', content: 'Duplicando contenido' },
      { letter: 'R', content: 'Preparar lanzamiento' },
      { letter: 'T', content: 'En 30 días' }
    ]
  },
  {
    id: 2,
    title: 'Incrementar ventas',
    description: 'Subir un 20% las ventas en 1 año ofreciendo nuevos productos.',
    smart: [
      { letter: 'S', content: 'Aumentar ventas en un 20%' },
      { letter: 'M', content: 'De 200.000 a 240.000' },
      { letter: 'A', content: 'Con nuevos productos' },
      { letter: 'R', content: 'Aprovechar base de clientes' },
      { letter: 'T', content: 'En 12 meses' }
    ]
  }
]);

const technicalGoals = ref<SmartGoal[]>([
  {
    id: 1,
    title: 'Optimizar carga',
    description: 'Reducir tiempo de carga de la app en un 30% antes de fin de mes.',
    smart: [
      { letter: 'S', content: 'Reducir tiempo de carga' },
      { letter: 'M', content: 'Un 30% menos' },
      { letter: 'A', content: 'Mejorando imágenes y caché' },
      { letter: 'R', content: 'Mejor experiencia de usuario' },
      { letter: 'T', content: 'Antes de fin de mes' }
    ]
  },
  {
    id: 2,
    title: 'Mejorar test coverage',
    description: 'Cubrir 90% del código con tests antes del próximo sprint.',
    smart: [
      { letter: 'S', content: 'Aumentar cobertura de tests' },
      { letter: 'M', content: '90% de código' },
      { letter: 'A', content: 'Escribiendo tests unitarios' },
      { letter: 'R', content: 'Aumentar fiabilidad del código' },
      { letter: 'T', content: 'Antes del próximo sprint' }
    ]
  }
]);
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

/* Contenedor principal */
.page-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 16px 0;
}

/* Secciones */
.section-container {
  margin-bottom: 32px;
}

.section-header {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.section-icon {
  font-size: 24px;
  padding: 12px;
  border-radius: 12px;
  margin-right: 16px;
  color: white;
}

.section-icon.business {
  background: linear-gradient(135deg, var(--ion-color-primary), var(--ion-color-primary-tint));
}

.section-icon.technical {
  background: linear-gradient(135deg, var(--ion-color-secondary), var(--ion-color-secondary-tint));
}

.section-header h2 {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: var(--ion-color-dark);
}

/* Acordeones */
.custom-accordion {
  margin-bottom: 16px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.accordion-header {
  --background: white;
  --border-color: transparent;
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
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background-color: var(--ion-color-primary);
  color: white;
  font-weight: 600;
  margin-right: 12px;
}

.accordion-content {
  background-color: white;
}

.kpi-description {
  color: #374151; /* Cambiar de var(--ion-color-medium) a un color más oscuro */
  margin-bottom: 16px;
  font-size: 15px;
  font-weight: 500; /* Añadir peso para mejor legibilidad */
}

/* Lista SMART */
.smart-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.smart-item {
  display: flex;
  align-items: center;
}

.smart-letter {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 8px;
  color: white;
  font-weight: 600;
  margin-right: 12px;
}

.letter-s {
  background-color: var(--ion-color-primary);
}

.letter-m {
  background-color: var(--ion-color-secondary);
}

.letter-a {
  background-color: var(--ion-color-tertiary);
}

.letter-r {
  background-color: var(--ion-color-success);
}

.letter-t {
  background-color: var(--ion-color-warning);
}

.smart-content {
  font-size: 15px;
  color: #111827; /* Cambiar de var(--ion-color-dark) a un color más oscuro y específico */
  font-weight: 500; /* Añadir peso para mejor legibilidad */
}

/* Estilos para el estado expandido/colapsado */
ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(var(--ion-color-primary-rgb), 0.05);
}

ion-accordion.accordion-expanding ion-item[slot='header'] .kpi-number,
ion-accordion.accordion-expanded ion-item[slot='header'] .kpi-number {
  background-color: var(--ion-color-primary-shade);
}
</style>
