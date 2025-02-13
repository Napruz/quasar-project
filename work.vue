<template>
  <ul class="month-list">
    <li v-for="month in monthTasks" :key="month.id" class="month-item">
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
              <div
                v-if="month.state === 'Выполнено'"
                class="adaptation-process-state row"
              >
                <p class="adaptation-state-value">{{ month.state }}</p>
                <q-icon
                  size="md"
                  name="fa-solid fa-square-check"
                  color="green"
                  style="margin: auto 0"
                />
              </div>
              <div v-else class="adaptation-process-state row">
                <p class="adaptation-state-value">{{ month.state }}</p>
                <q-icon
                  size="md"
                  name="fa-solid fa-square-check"
                  color="grey"
                  style="margin: auto 0"
                />
              </div>
            </div>
          </template>

          <q-card>
            <q-card-section class="main-process-container column">
              <ul class="month-tasks-list">
                <li
                  v-for="task in month.tasks"
                  :key="task.id"
                  class="month-task-item"
                >
                  <ul class="table-list">
                    <li class="table-row-item column">
                      {{ task.taskName }}
                    </li>
                    <li class="table-row-item column">
                      <q-toggle
                        v-model="task.completed"
                        :false-value="false"
                        :true-value="true"
                        label="Выполнено"
                        color="green"
                        unchecked-color="red"
                      />
                    </li>
                    <li class="table-row-item column">
                      <q-toggle
                        :model-value="task.completed"
                        label="Не выполнено"
                        color="green"
                        disable
                      />
                    </li>
                    <li class="table-row-item">
                      {{ task.dueDate }}
                    </li>
                    <li class="table-row-item">
                      {{ task.actualDueDate }}
                    </li>
                    <li class="table-row-item">
                      {{ task.comment }}
                    </li>
                  </ul>
                </li>
              </ul>

              <q-btn
                class="add-task-button"
                label="Добавить задачу"
                color="secondary"
                @click="openDialog(month.id)"
              />

              <!-- Диалоговое окно для добавления задачи -->
              <q-dialog v-model="isDialogOpen" persistent>
                <q-card style="min-width: 400px;">
                  <q-card-section>
                    <div class="q-gutter-md">
                      <q-input
                        v-model="newTask.taskName"
                        label="Описание задачи"
                        type="text"
                        outlined
                        required
                      />
                      <q-input
                        v-model="newTask.dueDate"
                        label="Срок исполнения"
                        type="date"
                        outlined
                        required
                      />
                    </div>
                  </q-card-section>

                  <q-card-actions align="right">
                    <q-btn
                      flat
                      label="Отмена"
                      color="grey"
                      @click="closeDialog"
                    />
                    <q-btn
                      flat
                      label="Сохранить"
                      color="secondary"
                      @click="saveTask"
                    />
                  </q-card-actions>
                </q-card>
              </q-dialog>
            </q-card-section>
          </q-card>
        </q-expansion-item>
      </div>
    </li>
  </ul>
</template>

<script setup>
import { ref } from "vue";

const monthTasks = ref([
  {
    id: 145,
    title: "Первый месяц работы",
    state: "Выполнено",
    tasks: [
      {
        id: 1145,
        taskName: "Успешное прохождение экзамена",
        completed: true,
        canChange: true,
        completedMentor: true,
        mentorCanChange: true,
        dueDate: "12.03.2025",
        actualDueDate: "31.03.2025",
        comment: "Комментарий к задаче",
      },
    ],
  },
  {
    id: 222,
    title: "Второй месяц работы",
    state: "Не выполнено",
    tasks: [],
  },
]);

const isDialogOpen = ref(false);
const currentMonthId = ref(null);

const newTask = ref({
  taskName: "",
  dueDate: "",
  completed: false,
  canChange: true,
  completedMentor: false,
  mentorCanChange: true,
  actualDueDate: "",
  comment: "",
});

// Открытие диалога
const openDialog = (monthId) => {
  currentMonthId.value = monthId;
  isDialogOpen.value = true;
};

// Закрытие диалога
const closeDialog = () => {
  isDialogOpen.value = false;
  resetNewTask();
};

// Сохранение задачи
const saveTask = () => {
  if (newTask.value.taskName && newTask.value.dueDate) {
    const month = monthTasks.value.find((m) => m.id === currentMonthId.value);
    if (month) {
      const newTaskId = Date.now();
      month.tasks.push({ ...newTask.value, id: newTaskId });
    }
    closeDialog();
  }
};

// Сброс формы новой задачи
const resetNewTask = () => {
  newTask.value = {
    taskName: "",
    dueDate: "",
    completed: false,
    canChange: true,
    completedMentor: false,
    mentorCanChange: true,
    actualDueDate: "",
    comment: "",
  };
};
</script>
