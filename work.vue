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

const newTaskText = ref("");
const newTaskDate = ref("");
const selectedMonth = ref(null); // Например, если выбираешь месяц в другом месте

const saveTask = () => {
  if (!newTaskText.value || !newTaskDate.value) {
    showToast("Заполните все поля");
    return;
  }

  addNewTask(selectedMonth.value, newTaskText.value, newTaskDate.value);
  
  closeModal();
  clearModal();
};

const clearModal = () => {
  newTaskText.value = "";
  newTaskDate.value = "";
  isModalOpen.value = false;
};

updateLocalNewTaskList(taskMonth, taskText, taskDate) {
  const month = this.cabinetData.find(m => m.id === taskMonth);
  if (month) {
    month.tasks.push({
      id: Date.now(), // Временный id
      name: taskText,
      date: taskDate,
      completed: false
    });
  }
}
