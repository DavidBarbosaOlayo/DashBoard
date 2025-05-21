<template>
  <div :class="['spark-card', bgColor]">
    <div class="header">
      <ion-icon :icon="getIcon()"></ion-icon>
      <span>{{ title }}</span>
    </div>
    <div class="value">{{ value }}</div>
    <apex-chart type="area" height="60" :options="chartOptions" :series="chartSeries" />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import ApexChart from 'vue3-apexcharts'
import { IonIcon } from '@ionic/vue'
import { 
  navigateOutline, 
  eyeOutline, 
  peopleOutline,
  analyticsOutline
} from 'ionicons/icons'

const props = defineProps<{
  title: string
  value: string
  iconName: string
  bgColor: string        /* gradient-blue / -pink / -orange */
  chartOptions: any
  chartSeries: any
}>()

// Función para obtener el icono correcto de Ionicons
const getIcon = () => {
  switch (props.iconName) {
    case 'navigate-outline': return navigateOutline
    case 'eye-outline': return eyeOutline
    case 'people-outline': return peopleOutline
    default: return analyticsOutline
  }
}
</script>

<style scoped>
.spark-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
  height: 100%;
  padding: 16px;
  border-radius: 12px;
  overflow: hidden;
  position: relative;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.header {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #fff;
  z-index: 1;
}

.header ion-icon {
  font-size: 20px;
}

.value {
  font-size: 28px;
  font-weight: 700;
  color: #fff;
  margin: 8px 0;
  z-index: 1;
}

/* Gradientes mejorados */
.gradient-blue {
  background: linear-gradient(135deg, #3b82f6, #1d4ed8);
}

.gradient-pink {
  background: linear-gradient(135deg, #ec4899, #be185d);
}

.gradient-orange {
  background: linear-gradient(135deg, #f97316, #c2410c);
}

/* Overlay para mejorar la legibilidad del gráfico */
.spark-card::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(to bottom, rgba(0, 0, 0, 0.1) 0%, rgba(0, 0, 0, 0.3) 100%);
  pointer-events: none;
}

/* Asegurarse de que el gráfico esté en la parte inferior */
:deep(.apexcharts-canvas) {
  position: relative;
  z-index: 0;
}
</style>