<template>
  <q-card>
    <q-card-section class="main-process-container column">
      <!-- Список задач по месяцам -->
      <ul class="month-tasks-list">
        <li v-for="(month, monthIndex) in monthTasks" :key="monthIndex" class="month-task-item">
          <h3>{{ month.title }}</h3>
          <p>Статус: {{ month.state }}</p>

          <ul class="table-list">
            <li v-for="(task, taskIndex) in month.tasks" :key="taskIndex" class="table-row-item column">
              <div>
                <strong>{{ task.taskName }}</strong>
                <p>Дата выполнения: {{ task.dueDate }}</p>
                <p>Комментарий: {{ task.comment }}</p>
                <q-toggle
                  v-if="task.canChange"
                  v-model="task.completed"
                  :false-value="false"
                  :true-value="true"
                  label="Выполнено"
                  color="green"
                  unchecked-color="red"
                />
              </div>
            </li>
          </ul>

          <!-- Кнопка для добавления новой задачи в текущий месяц -->
          <q-btn 
            class="comment-button" 
            label="Добавить задачу" 
            color="secondary" 
            @click="startCreatingTask(monthIndex)"
          />
        </li>
      </ul>

      <!-- Форма создания новой задачи -->
      <q-dialog v-model="isCreatingTask">
        <q-card>
          <q-card-section>
            <div class="q-gutter-md">
              <q-input
                v-model="newTask.taskName"
                label="Описание задачи"
                type="text"
                outlined
              />
              <q-input
                v-model="newTask.dueDate"
                label="Дата исполнения"
                type="date"
                outlined
              />
              <q-input
                v-model="newTask.comment"
                label="Комментарий"
                type="text"
                outlined
              />
            </div>
          </q-card-section>

          <q-card-actions align="right">
            <q-btn flat label="Отмена" color="negative" @click="isCreatingTask = false" />
            <q-btn flat label="Сохранить" color="positive" @click="saveTask" />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </q-card-section>
  </q-card>
</template>

<script setup>
import { ref } from "vue";

const monthTasks = ref([
  {
    title: "Первый месяц работы",
    state: "Выполнено",
    tasks: []
  },
  {
    title: "Второй месяц работы",
    state: "Не выполнено",
    tasks: []
  },
  {
    title: "Третий месяц работы",
    state: "Не выполнено",
    tasks: []
  }
]);

const isCreatingTask = ref(false);
const currentMonthIndex = ref(null);

const newTask = ref({
  taskName: "",
  completed: false,
  canChange: true,
  completedMentor: false,
  mentorCanChange: true,
  dueDate: "",
  comment: ""
});

const startCreatingTask = (monthIndex) => {
  currentMonthIndex.value = monthIndex;
  isCreatingTask.value = true;
};

const saveTask = () => {
  if (newTask.value.taskName.trim() !== "" && newTask.value.dueDate.trim() !== "") {
    monthTasks.value[currentMonthIndex.value].tasks.push({ ...newTask.value });
    newTask.value = {
      taskName: "",
      completed: false,
      canChange: true,
      completedMentor: false,
      mentorCanChange: true,
      dueDate: "",
      comment: ""
    };
    isCreatingTask.value = false;
  }
};
</script>

<style scoped>
.month-tasks-list {
  list-style-type: none;
  padding: 0;
}

.month-task-item {
  margin: 20px 0;
  padding: 15px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.table-list {
  list-style-type: none;
  padding: 0;
}

.table-row-item {
  margin: 10px 0;
  padding: 10px;
  border-bottom: 1px solid #e0e0e0;
}
</style>
