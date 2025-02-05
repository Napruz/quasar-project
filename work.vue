<template>
  <div class="process-container column">
    <h3 class="adaptation-process-title">Процесс адаптации</h3>

    <ul class="adaptation-process-list column">
      <li v-for="(process, processIndex) in processes" :key="processIndex" class="adaptation-process-item row">
        <q-expansion-item
          expand-separator
          switch-toggle-side
          :label="process.title"
          class="expansion-item-wrapper"
        >
          <q-card>
            <q-card-section class="main-process-container column">
              <div class="small-process-container row" style="justify-content: space-between">
                <div class="toogle-wrapper column">
                  <ul class="stages-list">
                    <li v-for="(task, taskIndex) in process.tasks" :key="taskIndex" class="stages-item">
                      <div class="duration-wrapper">
                        <div class="process-description-wrapper row">
                          <q-icon size="xs" name="fa-solid fa-circle-arrow-right" color="grey" />
                          <p class="process-description row">{{ task.description }}</p>
                        </div>
                        <p class="duration-value-wrapper row text-grey">
                          <q-icon size="12px" name="fa-solid fa-clock" color="grey" />
                          <span class="duration-value text-grey">{{ task.duration }}</span>
                        </p>
                      </div>

                      <q-toggle
                        v-model="task.completed"
                        :false-value="false"
                        :true-value="true"
                        :label="task.completed ? 'Выполнено' : 'Не выполнено'"
                        color="green"
                        unchecked-color="red"
                      />
                    </li>
                  </ul>
                </div>
              </div>
            </q-card-section>
          </q-card>
        </q-expansion-item>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    const processes = ref([
      {
        title: "Знакомство с командой",
        tasks: [
          {
            description: "Я принял участие во встрече с руководителем и помощником по адаптации",
            duration: "1-2 рабочий день",
            completed: false,
          },
          {
            description: "Я познакомился с командой",
            duration: "1-2 рабочий день",
            completed: false,
          },
        ],
      },
      {
        title: "Задачи на испытательный срок",
        tasks: [
          {
            description: "Получите от руководителя задачи на испытательный срок",
            duration: "1-2 рабочая неделя",
            completed: false,
          },
        ],
      },
    ]);

    return {
      processes,
    };
  },
};
</script>

