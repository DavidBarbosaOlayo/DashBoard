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
            <ion-note>Dashboard</ion-note>
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
  --ion-color-warning-shade: #d97706;
  --ion-color-warning-tint: #f6a823;

  --ion-color-danger: #ef4444;
  --ion-color-danger-rgb: 239, 68, 68;
  --ion-color-danger-contrast: #ffffff;
  --ion-color-danger-contrast-rgb: 255, 255, 255;
  --ion-color-danger-shade: #d23c3c;
  --ion-color-danger-tint: #f15757;

  /* Grises de negocio */
  --color-business-gray: #6B7280;       /* para iconos, textos activos */
  --color-business-gray-dark: #4B5563;  /* para fondo del menú */
  --color-business-gray-light: #9CA3AF; /* para hover o estados suaves */

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

/* 1) Fondo de la barra lateral */
ion-menu {
  --background: var(--color-business-gray-dark);
}

/* 2) Cabecera del menú */
.menu-header {
  padding: 24px 16px 16px;
  background: var(--color-business-gray);
  color: white;
  margin-bottom: 8px;
}

/* 3) Icono y texto del logo */
.logo-icon,
.logo span {
  color: white;
}

/* 4) Color de los items del menú */
ion-list {
  padding: 8px;
}

ion-item {
  --padding-start: 16px;
  --padding-end: 16px;
  --min-height: 48px;
  margin-bottom: 4px;
  --border-radius: 8px;
  --background: transparent;
  --color: var(--color-business-gray);
  --background-hover: var(--color-business-gray-light);
  font-weight: 500;
}

ion-item ion-icon {
  color: inherit;
}

/* 5) Item seleccionado */
ion-item.selected {
  --background: rgba(107, 114, 128, 0.2);
  --color: white;
}

ion-item.selected ion-icon {
  color: white;
}

/* 6) Footer del menú */
.menu-footer {
  padding: 8px;
  position: absolute;
  bottom: 0;
  width: 100%;
  border-top: 1px solid rgba(255,255,255,0.1);
}

.menu-footer ion-item {
  --color: var(--color-business-gray-light);
}
</style>
