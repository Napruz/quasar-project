<template>
  <ul class="adaptation-process-list column">
    <li v-for="(process, processIndex) in processes" :key="processIndex" class="adaptation-process-item row">
      <q-expansion-item expand-separator switch-toggle-side :label="process.title" class="expansion-item-wrapper">
        <q-card>
          <q-card-section class="main-process-container column">
            <div class="small-process-container row">
              <q-tabs v-model="selectedTab" dense class="text-grey">
                <q-tab name="all" label="Все задачи" />
                <q-tab name="pending" label="Невыполненные задачи" />
              </q-tabs>
              <q-tab-panels v-model="selectedTab" animated>
                <q-tab-panel name="all">
                  <ul class="stages-list">
                    <li v-for="(task, taskIndex) in process.tasks" :key="taskIndex" class="stages-item">
                      <div class="duration-wrapper">
                        <div class="process-description-wrapper row">
                          <q-icon size="xs" name="fa-solid fa-circle-arrow-right" color="grey" class="process-description-icon" />
                          <p class="process-description row">{{ task.description }}</p>
                        </div>
                        <q-toggle v-model="task.completed" :false-value="false" :true-value="true" 
                                  :label="task.completed ? 'Выполнено' : 'Не выполнено'" 
                                  color="green" unchecked-color="red" 
                                  @update:model-value="showToast(task.description)" />
                      </div>
                    </li>
                  </ul>
                </q-tab-panel>
                <q-tab-panel name="pending">
                  <ul class="stages-list">
                    <li v-for="(task, taskIndex) in pendingTasks(process.tasks)" :key="taskIndex" class="stages-item">
                      <div class="duration-wrapper">
                        <div class="process-description-wrapper row">
                          <q-icon size="xs" name="fa-solid fa-circle-arrow-right" color="grey" class="process-description-icon" />
                          <p class="process-description row">{{ task.description }}</p>
                        </div>
                        <q-toggle v-model="task.completed" :false-value="false" :true-value="true" 
                                  :label="task.completed ? 'Выполнено' : 'Не выполнено'" 
                                  color="green" unchecked-color="red" 
                                  @update:model-value="showToast(task.description)" />
                      </div>
                    </li>
                  </ul>
                </q-tab-panel>
              </q-tab-panels>
            </div>
          </q-card-section>
        </q-card>
      </q-expansion-item>
    </li>
  </ul>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    const selectedTab = ref("all");
    const processes = ref([
      {
        title: "Знакомство с командой",
        tasks: [
          { description: "Я принял участие во встрече", completed: false },
          { description: "Я познакомился с командой", completed: true }
        ]
      }
    ]);
    const showToast = (message) => {
      console.log("Задача сохранена: ", message);
    };
    const pendingTasks = (tasks) => tasks.filter(task => !task.completed);
    
    return {
      selectedTab,
      processes,
      showToast,
      pendingTasks
    };
  }
};
</script>
