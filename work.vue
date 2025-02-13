<q-card>
  <q-card-section class="main-process-container column">

    <!-- Список задач -->
    <ul class="month-tasks-list">
      <li v-for="(task, index) in monthTasks" :key="index" class="month-task-item">
        <ul class="table-list">
          <li class="table-row-item column">
            {{ task.description }}
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
            {{ task.duration }}
          </li>
        </ul>
      </li>
    </ul>

    <!-- Кнопка для добавления новой задачи -->
    <q-btn 
      class="comment-button" 
      label="Добавить задачу" 
      color="secondary" 
      @click="isCreatingTask = true"
    />

    <!-- Форма создания новой задачи -->
    <q-dialog v-model="isCreatingTask">
      <q-card>
        <q-card-section>
          <div class="q-gutter-md">
            <q-input
              v-model="newTask.description"
              label="Описание задачи"
              type="text"
              outlined
            />
            <q-input
              v-model="newTask.duration"
              label="Срок исполнения"
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

import { ref } from "vue";

export default {
  setup() {
    // Список задач
    const monthTasks = ref([
      {
        description: "Я принял участие во встрече с руководителем",
        duration: "1-2 рабочий день",
        completed: false,
      },
      {
        description: "Получите от руководителя задачи на испытательный срок",
        duration: "1-2 рабочая неделя",
        completed: false,
      },
    ]);

    // Стейт для управления отображением формы
    const isCreatingTask = ref(false);

    // Новая задача
    const newTask = ref({
      description: "",
      duration: "",
      completed: false,
    });

    // Метод сохранения новой задачи
    const saveTask = () => {
      if (newTask.value.description.trim() !== "" && newTask.value.duration.trim() !== "") {
        monthTasks.value.push({ ...newTask.value });
        newTask.value = { description: "", duration: "", completed: false };
        isCreatingTask.value = false;
      }
    };

    return {
      monthTasks,
      isCreatingTask,
      newTask,
      saveTask,
    };
  },
};

.month-tasks-list {
  list-style-type: none;
  padding: 0;
}

.month-task-item {
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

