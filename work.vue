<q-btn
  class="add-task-button"
  label="Добавить задачу"
  color="secondary"
  @click="openDialog(month.id)"
/>

<!-- Форма создания новой задачи -->
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
      <q-btn flat label="Отмена" color="grey" @click="isDialogOpen = false" />
      <q-btn flat label="Сохранить" color="secondary" @click="saveTask()" />
    </q-card-actions>
  </q-card>
</q-dialog>

const isDialogOpen = ref(false); // Стейт для управления диалогом
const currentMonthId = ref(null); // ID текущего месяца

// Новая задача
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

// Открытие диалога и запоминание ID месяца
const openDialog = (monthId) => {
  currentMonthId.value = monthId;
  isDialogOpen.value = true;
};

// Метод сохранения новой задачи
const saveTask = () => {
  if (newTask.value.taskName.trim() !== "" && newTask.value.dueDate.trim() !== "") {
    // Находим нужный месяц по ID
    const month = monthTasks.value.find((m) => m.id === currentMonthId.value);
    if (month) {
      // Генерация уникального ID задачи
      const newTaskId = Date.now();
      month.tasks.push({ ...newTask.value, id: newTaskId });
    }
    // Сброс данных формы и закрытие диалога
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
    isDialogOpen.value = false;
  }
};
