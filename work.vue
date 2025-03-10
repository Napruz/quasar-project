const updateProcessState = () => {
  cabinetData.value.forEach((process) => {
    process.state = process.tasks.every(task => task.completed) ? 'Выполнено' : 'Не выполнено';
  });
};

const handleToggle = async (newValue, id) => {
  await postTaskState(newValue, id);
  showToast("Задача сохранена");
  updateLocalTask(id, newValue);
  updateProcessState(); // Пересчитываем состояние процесса
};

onMounted(async () => {
  await fetchCabinetData();
  updateProcessState();
});

