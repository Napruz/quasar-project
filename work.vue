const updateMonthState = (month) => {
  month.state = month.tasks.every(task => task.completed) ? 'Выполнено' : 'Не выполнено';
};

const handleToggle = async (newValue, id, month) => {
  await postTaskState(newValue, id);
  updateLocalTask(id, newValue);
  updateMonthState(month); // Обновляем только этот месяц
  showToast("Задача сохранена");
};

<q-toggle
  v-if="task.canChange"
  v-model="task.completed"
  :false-value="false"
  :true-value="true"
  :label="task.completed ? 'Выполнено' : 'Не выполнено'"
  color="green"
  @update:model-value="handleToggle(task.completed, task.id, month)"
  style="width: fit-content"
/>
