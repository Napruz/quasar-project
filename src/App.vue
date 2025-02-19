<q-btn
  class="add-task-button"
  label="Добавить задачу"
  color="secondary"
  @click="openModal(month.title)"
/>

<q-dialog v-model="isModalOpen">
  <q-card class="column" style="min-width: 500px; max-width: 600px;">
    <div class="row justify-between no-wrap">
      <span style="margin-top: 20px; margin-left: 20px;">
        Новая задача
      </span>

      <q-btn
        icon="close"
        flat
        dense
        round
        style="margin-left: auto; margin-right: 10px; margin-top: 10px;"
        @click="closeModal"
      />
    </div>

    <q-card-section>
      <div class="q-gutter-md column">
        <q-input
          v-model="newTaskText"
          label="Описание задачи"
          outlined
          required
          clearable
          type="textarea"
          autogrow
          :maxlength="1000"
        />

        <q-input
          v-model="newTaskDate"
          label="Срок исполнения"
          type="date"
          outlined
          required
          style="max-width: 200px;"
        />
      </div>
    </q-card-section>

    <q-card-actions align="right">
      <q-btn flat label="Отмена" color="grey" @click="clearModal" />
      <q-btn flat label="Сохранить" color="secondary" @click="saveTask()" />
    </q-card-actions>
  </q-card>
</q-dialog>


const isModalOpen = ref(false);
const selectedMonth = ref(""); // Хранит название выбранного месяца

const openModal = (monthTitle) => {
  selectedMonth.value = monthTitle; // Сохраняем выбранный месяц перед открытием модалки
  isModalOpen.value = true;
};

const saveTask = () => {
  console.log("Выбранный месяц:", selectedMonth.value);

  const monthMap = {
    "Первый месяц работы": "1",
    "Второй месяц работы": "2",
    "Третий месяц работы": "3",
  };

  if (monthMap[selectedMonth.value]) {
    selectedMonth.value = monthMap[selectedMonth.value];
  } else {
    showToast("Некорректное название месяца");
    return;
  }

  if (!newTaskText.value.trim() || !newTaskDate.value.trim()) {
    showToast("Заполните все поля");
    return;
  }

  addNewTask(selectedMonth.value, newTaskText.value, newTaskDate.value);
  closeModal();
  clearModal();
};
