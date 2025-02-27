<template>
  <div class="process-container column">
    <div class="process-title-wrapper row">
      <h3 class="adaptation-process-title">Процесс адаптации</h3>

      <q-btn
        color="primary"
        label="Развернуть все"
        size="sm"
        @click="unWrapProcessList"
      />
    </div>

    <!-- Список всех процессов адаптации -->
    <ul class="adaptation-process-list column">
      <li
        v-for="process in cabinetData"
        :key="process.id"
        class="adaptation-process-item row"
      >
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
                  <div class="tasks-wrapper">
                    <!-- Заголовки табов -->
                    <div class="process-tab-container row">
                      <p
                        class="process-tab-item"
                        :class="{
                          'process-tab-current': activeTab.value === 'all',
                        }"
                        @click="activeTab.value = 'all'"
                      >
                        Все задачи
                      </p>
                      <p
                        class="process-tab-item"
                        :class="{
                          'process-tab-current': activeTab.value === 'pending',
                        }"
                        @click="activeTab.value = 'pending'"
                      >
                        Невыполненные задачи
                      </p>
                    </div>

                    <!-- Список задач внутри процесса -->
                    <div v-if="activeTab.value === 'all'" class="stages-list-wrapper">
                      <ul class="stages-list">
                        <li
                          v-for="task in process.tasks"
                          :key="task.id"
                          class="stages-item"
                        >
                          <div class="duration-wrapper">
                            <div class="process-description-wrapper row">
                              <q-icon
                                size="xs"
                                name="fa-solid fa-circle-arrow-right"
                                color="grey"
                                class="process-description-icon"
                              />
                              <p class="process-description row">
                                {{ task.description }}
                              </p>
                            </div>
                          </div>
                        </li>
                      </ul>
                    </div>

                    <!-- Список невыполненных задач -->
                    <div v-if="activeTab.value === 'pending'" class="stages-list-wrapper">
                      <ul
                        v-if="process.tasks.filter((task) => !task.completed).length !== 0"
                        class="stages-list"
                      >
                        <li
                          v-for="task in process.tasks.filter((task) => !task.completed)"
                          :key="task.id"
                          class="stages-item"
                        >
                          <div class="duration-wrapper">
                            <div class="process-description-wrapper row">
                              <q-icon
                                size="xs"
                                name="fa-solid fa-circle-arrow-right"
                                color="grey"
                                class="process-description-icon"
                              />
                              <p class="process-description row">
                                {{ task.description }}
                              </p>
                            </div>
                          </div>
                        </li>
                      </ul>
                      <ul v-else class="stages-list">
                        <li class="stages-item">
                          <p class="text-grey" style="margin: 0 auto">
                            Все задачи выполнены
                          </p>
                        </li>
                      </ul>
                    </div>
                  </div>
                </div>
              </div>
            </q-card-section>
          </q-card>
        </q-expansion-item>

        <!-- Состояние выполнения всего процесса -->
        <div v-if="process.state === 'Выполнено'" class="adaptation-process-state column">
          <q-icon
            size="md"
            name="fa-solid fa-square-check"
            color="green"
            style="margin: 0 auto"
          />
          <p class="adaptation-state-value row nowrap">{{ process.state }}</p>
        </div>
        <div v-else class="adaptation-process-state column">
          <q-icon
            size="md"
            name="fa-solid fa-square-check"
            color="grey"
            style="margin: 0 auto"
          />
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
    const activeTab = ref('all'); // Переменная для активной вкладки
    const expandedProcesses = ref({}); // Хранение состояния раскрытых процессов

    const cabinetData = ref([
      // Пример данных, можно заменить на реальные
      { id: 1, title: 'Процесс 1', tasks: [{ id: 1, description: 'Задача 1', completed: false }] },
      { id: 2, title: 'Процесс 2', tasks: [{ id: 2, description: 'Задача 2', completed: true }] },
    ]);

    // Функция для раскрытия всех процессов
    const unWrapProcessList = () => {
      cabinetData.value.forEach((process) => {
        expandedProcesses.value[process.id] = true; // Разворачиваем все процессы
      });
    };

    return {
      activeTab,
      expandedProcesses,
      cabinetData,
      unWrapProcessList,
    };
  },
};
</script>
