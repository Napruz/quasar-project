<template>
  <ul class="month-list">
    <li class="month-item" v-for="(month, monthIndex) in months" :key="monthIndex">
      <div class="month-wrapper row" style="flex-wrap: nowrap">
        <q-expansion-item
          expand-separator
          switch-toggle-side
          :label="month.title"
          class="expansion-item-wrapper"
        >
          <template v-slot:header>
            <div class="header-content">
              <span>{{ month.title }}</span>
            </div>
          </template>

          <q-card>
            <q-card-section class="main-process-container column">
              <!-- Список задач в месяце -->
              <ul class="month-tasks-list">
                <li class="month-task-item" v-for="(task, taskIndex) in month.tasks" :key="taskIndex">
                  <ul class="table-list">
                    <li class="table-row-item column" v-for="(block, blockIndex) in task.blocks" :key="blockIndex">
                      {{ block }}
                    </li>
                  </ul>
                </li>
              </ul>
            </q-card-section>
          </q-card>
        </q-expansion-item>
      </div>
      <!-- Форма для добавления новой задачи -->
      <q-form @submit="addTask(monthIndex)">
        <q-input
          v-for="blockIndex in 6"
          :key="blockIndex"
          v-model="newTask.blocks[blockIndex - 1]"
          label="Блок {{ blockIndex }}"
          outlined
          dense
          style="margin-bottom: 10px"
        />
        <q-btn type="submit" label="Добавить задачу" color="primary" />
      </q-form>
    </li>
  </ul>
</template>

import { ref } from "vue";

export default {
  setup() {
    const months = ref([
      {
        title: "Первый месяц работы",
        tasks: []
      },
      {
        title: "Второй месяц работы",
        tasks: []
      },
      {
        title: "Третий месяц работы",
        tasks: []
      }
    ]);

    const newTask = ref({
      blocks: Array(6).fill("")
    });

    const addTask = (monthIndex) => {
      if (newTask.value.blocks.some((block) => block.trim() === "")) {
        alert("Заполните все блоки");
        return;
      }

      months.value[monthIndex].tasks.push({ blocks: [...newTask.value.blocks] });

      newTask.value.blocks = Array(6).fill("");
    };

    return {
      months,
      newTask,
      addTask
    };
  }
};
