const updateLocalNewTaskList = (taskMonth, taskText, taskDate) => {
  const month = cabinetData.value.find((m) => m.title === taskMonth);

  if (month) {
    month.tasks.push({
      id: `temp-${Date.now()}`, // Временный ID (до перезагрузки)
      taskName: taskText,
      dueDate: taskDate,
      completed: false,
      canChange: true,
      completedMentor: false,
    });
  } else {
    console.warn(`Не найден месяц: ${taskMonth}`);
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
        { name: "action_name", value: `add_${taskMonth}_mounth_task` },
        { name: "task_name", value: taskText },
        { name: "task_date", value: taskDate },
        { name: "task_comment", value: "" },
      ],
    };

    const formData = new FormData();
    formData.append("action", JSON.stringify(requestBody));

    const queryString = new URLSearchParams({ secid: wtSecId }).toString();
    await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
      headers: { "Content-Type": "multipart/form-data" },
    });

    updateLocalNewTaskList(taskMonth, taskText, taskDate);
    showToast("Задача добавлена");
  } catch (error) {
    console.error("Ошибка при добавлении задачи", error);
    showToast("Ошибка при добавлении задачи");
  }
};

const addNewTask = async (taskMonth, taskText, taskDate) => {
  await postNewTask(taskMonth, taskText, taskDate);
};
