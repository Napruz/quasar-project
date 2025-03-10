const updateProcessState = (process) => {
  process.state = process.tasks.every(task => task.completed) ? 'Выполнено' : 'Не выполнено';
};

const handleToggle = async (newValue, id, process) => {
  await postTaskState(newValue, id);
  showToast("Задача сохранена");
  updateLocalTask(id, newValue);
  updateProcessState(process); // Теперь обновляем только конкретный процесс
};

<q-toggle
  v-if="task.canChange"
  v-model="task.completed"
  :false-value="false"
  :true-value="true"
  :label="task.completed ? 'Выполнено' : 'Не выполнено'"
  color="green"
  @update:model-value="handleToggle(task.completed, task.id, process)"
  style="width: fit-content"
/>
