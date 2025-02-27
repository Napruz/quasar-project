<template>
  <div class="process-container column">
    <div class="process-title-wrapper row">
      <h3 class="adaptation-process-title">Процесс адаптации</h3>

      <!-- Кнопка для Развернуть все / Свернуть все -->
      <q-btn
        color="primary"
        :label="allExpanded ? 'Свернуть все' : 'Развернуть все'"
        size="sm"
        @click="toggleProcessList"
      />
    </div>

    <!-- Список всех процессов адаптации -->
    <ul class="adaptation-process-list column">
      <li v-for="process in cabinetData" :key="process.id" class="adaptation-process-item row">
        <!-- Название процесса -->
        <q-expansion-item
          v-model="expandedProcesses[process.id]"
          expand-separator
          switch-toggle-side
          :label="process.title"
          class="expansion-item-wrapper"
        >
          <q-card>
            <q-card-section class="main-process-container column">
              <div class="small-process-container row">
                <div class="toggle-wrapper column">
                  <!-- Блок для задачи и прочее содержимое -->
                </div>
              </div>
            </q-card-section>
          </q-card>
        </q-expansion-item>

        <!-- Состояние выполнения процесса (этапа) -->
        <div v-if="process.state === 'Выполнено'" class="adaptation-process-state column">
          <q-icon size="md" name="fa-solid fa-square-check" color="green" style="margin: 0 auto" />
          <p class="adaptation-state-value row nowrap">{{ process.state }}</p>
        </div>

        <div v-else class="adaptation-process-state column">
          <q-icon size="md" name="fa-solid fa-square-check" color="grey" style="margin: 0 auto" />
          <p class="adaptation-state-value row nowrap">{{ process.state }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const cabinetData = ref([]); // Данные процесса, приходят с сервера
    const expandedProcesses = ref({}); // Состояние раскрытых процессов
    const allExpanded = ref(false); // Флаг для отслеживания, развернуты ли все процессы

    // Функция для переключения состояния раскрытия всех процессов
    const toggleProcessList = () => {
      // Если все процессы уже раскрыты, сворачиваем их
      if (allExpanded.value) {
        cabinetData.value.forEach((process) => {
          expandedProcesses.value[process.id] = false; // Сворачиваем все
        });
      } else {
        // Иначе разворачиваем все
        cabinetData.value.forEach((process) => {
          expandedProcesses.value[process.id] = true; // Разворачиваем все
        });
      }

      // Переключаем флаг
      allExpanded.value = !allExpanded.value;
    };

    // Для демонстрации, замените это реальными данными с сервера
    cabinetData.value = [
      { id: '1', title: 'Процесс 1', state: 'Выполнено' },
      { id: '2', title: 'Процесс 2', state: 'Не выполнено' },
      { id: '3', title: 'Процесс 3', state: 'Выполнено' },
      // Дополнительные данные...
    ];

    return {
      cabinetData,
      expandedProcesses,
      allExpanded,
      toggleProcessList,
    };
  },
};
</script>

<style scoped>
/* Ваши стили */
</style>
