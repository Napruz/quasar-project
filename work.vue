<template>
  <!-- ... остальной код ... -->
    <div class="panel-container q-mt-sm">
      <div class="main-panel column">
        <!-- Основной контент -->
      </div>
      
      <AdaptationMaterials
        :boss-materials="bossMaterials"
        :assistant-materials="assistantMaterials"
        :new-employee-materials="newEmployeeMaterials"
      />
    </div>
  <!-- ... остальной код ... -->
</template>

<script setup>
import { ref, onMounted } from 'vue';
import AdaptationMaterials from '@/components/AdaptationMaterials.vue';

const bossMaterials = ref([]);
const assistantMaterials = ref([]);
const newEmployeeMaterials = ref([]);

onMounted(async () => {
  // Загружаем данные
  const response = await fetchMaterials();
  bossMaterials.value = response.bossMaterials;
  assistantMaterials.value = response.assistantMaterials;
  newEmployeeMaterials.value = response.newEmployeeMaterials;
});
</script>


<template>
  <div class="side-panel column">
    <div class="materials-wrapper column">
      <h3 class="materials-title">Материалы по адаптации</h3>

      <!-- Материалы для различных категорий -->
      <ul class="materials-link-list column">
        <!-- Для Руководителя -->
        <li class="materials-link-item">
          <q-expansion-item
            expand-separator
            switch-toggle-side
            label="Для руководителя"
            class="expansion-item-wrapper"
          >
            <q-card class="materials-card">
              <q-card-section class="column">
                <MaterialsList :materials="bossMaterials" />
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </li>

        <!-- Для Помощника -->
        <li class="materials-link-item">
          <q-expansion-item
            expand-separator
            switch-toggle-side
            label="Для помощника"
            class="expansion-item-wrapper"
          >
            <q-card class="materials-card">
              <q-card-section class="column">
                <MaterialsList :materials="assistantMaterials" />
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </li>

        <!-- Для нового работника -->
        <li class="materials-link-item">
          <q-expansion-item
            expand-separator
            switch-toggle-side
            label="Для нового работника"
            class="expansion-item-wrapper"
          >
            <q-card class="materials-card">
              <q-card-section class="column">
                <MaterialsList :materials="newEmployeeMaterials" />
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </li>
      </ul>
    </div>

    <SupportBlock />
  </div>
</template>

<script setup>
import { defineProps } from 'vue';
import MaterialsList from './MaterialsList.vue';
import SupportBlock from './SupportBlock.vue';

const props = defineProps({
  bossMaterials: {
    type: Array,
    default: () => []
  },
  assistantMaterials: {
    type: Array,
    default: () => []
  },
  newEmployeeMaterials: {
    type: Array,
    default: () => []
  }
});
</script>

<style scoped>
/* Переносим сюда все стили, относящиеся к боковой панели */
.side-panel {
  width: 350px;
  margin-left: 20px;
}

.materials-wrapper {
  margin-bottom: 30px;
}

.materials-title {
  font-size: 18px;
  font-weight: 700;
  margin: 0 0 15px 0;
}

.materials-link-list {
  list-style-type: none;
  padding-left: 0;
  margin: 0;
}

.materials-link-item {
  margin-bottom: 10px;
}

.materials-card {
  box-shadow: none;
}
</style>
