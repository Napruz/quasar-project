setup() {
  const newTaskText = ref("");
  const newTaskDate = ref("");
  const selectedMonth = ref("1");
  const isModalOpen = ref(false);

  const toastVisible = ref(false);
  const toastMessage = ref("");

  const showToast = (message = "Задача сохранена") => {
    toastMessage.value = message;
    toastVisible.value = true;
    setTimeout(() => {
      toastVisible.value = false;
    }, 2000);
  };

  const cabinetData = ref([]); // Хранилище задач

  const updateLocalNewTaskList = (taskMonth, taskText, taskDate) => {
    const month = cabinetData.value.find(m => m.id === taskMonth);
    if (month) {
      month.tasks.push({
        id: Date.now(), // Временный id
        name: taskText,
        date: taskDate,
        completed: false
      });
    }
  };

  const postNewTask = async (taskMonth, taskText, taskDate) => {
    try {
      const requestBody = {
        action: "eval_action",
        remote_action_id: "7472730402329554549",
        wvars: [
          { name: "_object_id", value: "" },
          { name: "_secid", value: wtSecId },
          { name: "user_id", value: "" },
          { name: "adaptation_id", value: "" },
          { name: "role", value: "collaborator" },
          { name: "action_name", value: `"add_${taskMonth}_mounth_task"` },
          { name: "task_name", value: taskText },
          { name: "task_date", value: taskDate },
          { name: "task_comment", value: "1" }
        ]
      };

      const formData = new FormData();
      formData.append("action", JSON.stringify(requestBody));

      const queryString = new URLSearchParams({ secid: wtSecId }).toString();
      const response = await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
        headers: { "Content-Type": "multipart/form-data" }
      });

      console.log("Данные с сервера:", response.data.results);
    } catch (error) {
      console.error("Ошибка при добавлении задачи:", error);
      showToast("Ошибка при добавлении задачи");
    }
  };

  const addNewTask = async (taskMonth, taskText, taskDate) => {
    await postNewTask(taskMonth, taskText, taskDate);
    showToast("Задача добавлена");
    updateLocalNewTaskList(taskMonth, taskText, taskDate);
  };

  const saveTask = () => {
    if (!newTaskText.value.trim() || !newTaskDate.value.trim()) {
      showToast("Заполните все поля");
      return;
    }
    addNewTask(selectedMonth.value, newTaskText.value, newTaskDate.value);
    closeModal();
    clearModal();
  };

  const closeModal = () => {
    isModalOpen.value = false;
  };

  const clearModal = () => {
    newTaskText.value = "";
    newTaskDate.value = "";
    isModalOpen.value = false;
  };

  return {
    newTaskText,
    newTaskDate,
    selectedMonth,
    isModalOpen,
    toastVisible,
    toastMessage,
    showToast,
    saveTask,
    closeModal,
    clearModal
  };
}
