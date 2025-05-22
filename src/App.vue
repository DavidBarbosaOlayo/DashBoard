<template>
  <ion-app>
    <ion-split-pane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay">
        <ion-content>
          <div class="menu-header">
            <div class="logo">
              <ion-icon :icon="analyticsOutline" class="logo-icon"></ion-icon>
              <span>Agricolum</span>
            </div>
            <ion-note>Data Visualization</ion-note>
          </div>

          <ion-list id="inbox-list" lines="none">
            <ion-menu-toggle :auto-hide="false" v-for="(p, i) in appPages" :key="i">
              <ion-item
                :router-link="p.url"
                router-direction="root"
                lines="none"
                :detail="false"
                :class="{ selected: selectedIndex === i }"
                @click="selectedIndex = i"
              >
                <ion-icon slot="start" :icon="p.icon" />
                <ion-label>{{ p.title }}</ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>

          <div class="menu-footer">
            <ion-item lines="none" button>
              <ion-icon slot="start" :icon="settingsOutline" />
              <ion-label>Configuración</ion-label>
            </ion-item>
            <ion-item lines="none" button>
              <ion-icon slot="start" :icon="logOutOutline" />
              <ion-label>Cerrar sesión</ion-label>
            </ion-item>
          </div>
        </ion-content>
      </ion-menu>

      <ion-router-outlet id="main-content"></ion-router-outlet>
    </ion-split-pane>
  </ion-app>
</template>

<script setup lang="ts">
import {
  IonApp,
  IonContent,
  IonIcon,
  IonItem,
  IonLabel,
  IonList,
  IonMenu,
  IonMenuToggle,
  IonNote,
  IonRouterOutlet,
  IonSplitPane
} from '@ionic/vue';

import {
  rocketOutline,
  codeSlashOutline,
  statsChartOutline,
  settingsOutline,
  logOutOutline,
  analyticsOutline
} from 'ionicons/icons';

import { ref, onMounted, watch } from 'vue';
import { useRoute } from 'vue-router';

const selectedIndex = ref(0);

const appPages = [
  {
    title: 'Negocio',
    url: '/negocio',
    icon: rocketOutline
  },
  {
    title: 'Técnico',
    url: '/tecnico',
    icon: codeSlashOutline
  },
  {
    title: 'KPIs',
    url: '/kpis',
    icon: statsChartOutline
  }
];

const route = useRoute();

const updateSelectedIndex = () => {
  const currentPath = route.path;
  const index = appPages.findIndex(p => p.url === currentPath);
  if (index !== -1) selectedIndex.value = index;
};

onMounted(updateSelectedIndex);
watch(route, updateSelectedIndex);
</script>

<style>
/* Estilos globales */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

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

  --ion-font-family: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}

* {
  box-sizing: border-box;
}

/* Personalización de scrollbars */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
}

::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}
</style>

<style scoped>
ion-split-pane {
  --side-max-width: 280px;
}

/* Estilos para el menú */
ion-menu {
  --background: #ffffff;
}

.menu-header {
  padding: 24px 16px 16px;
  background: linear-gradient(135deg, var(--ion-color-primary), var(--ion-color-primary-shade));
  color: white;
  margin-bottom: 8px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 8px;
}

.logo-icon {
  font-size: 24px;
}

.logo span {
  font-size: 20px;
  font-weight: 600;
}

ion-note {
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
  margin-bottom: 8px;
}

/* Estilos para los items del menú */
ion-list {
  padding: 8px;
}

ion-item {
  --padding-start: 16px;
  --padding-end: 16px;
  --min-height: 48px;
  margin-bottom: 4px;
  --border-radius: 8px;
  --background-hover: rgba(var(--ion-color-primary-rgb), 0.08);
  font-weight: 500;
}

ion-item.selected {
  --background: rgba(var(--ion-color-primary-rgb), 0.1);
  --color: var(--ion-color-primary);
}

ion-item.selected ion-icon {
  color: var(--ion-color-primary);
}

/* Footer del menú */
.menu-footer {
  padding: 8px;
  position: absolute;
  bottom: 0;
  width: 100%;
  border-top: 1px solid rgba(0, 0, 0, 0.05);
}

.menu-footer ion-item {
  --color: var(--ion-color-medium);
}
</style>