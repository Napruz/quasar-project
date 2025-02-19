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
    
    // Создаем временный ID
    const tempId = `temp-${Date.now()}`;

    // Добавляем временную задачу в локальный список
    updateLocalNewTaskList(taskMonth, {
      id: tempId,
      taskName: taskText,
      dueDate: taskDate,
      completed: false,
      canChange: true,
      completedMentor: false,
      isTemporary: true, // флаг временной задачи
    });

    await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
      headers: { "Content-Type": "multipart/form-data" },
    });

    console.log("Задача отправлена на сервер");

    showToast("Задача добавлена");
    
    // После успешного ответа запрашиваем обновленные данные
    fetchCabinetData(); 
  } catch (error) {
    console.error("Ошибка при добавлении задачи", error);
    showToast("Ошибка при добавлении задачи");
  }
};


const updateLocalNewTaskList = (taskMonth, newTask) => {
  const month = cabinetData.value.find((m) => m.title === taskMonth);
  if (month) {
    month.tasks.push(newTask);
  } else {
    console.warn(`Не найден месяц: ${taskMonth}`);
  }
};


const fetchCabinetData = async () => {
  try {
    const params = {
      collection_code: "vtbl_adaptation_2025",
      parameters: "data_mode=collaborator_result_task_data",
    };

    const response = await axios.post(
      BACKEND_URL,
      new URLSearchParams(params).toString()
    );

    // Удаляем временные задачи и заменяем их на реальные
    cabinetData.value = response.data.results.map((month) => ({
      ...month,
      tasks: month.tasks.filter((task) => !task.isTemporary),
    }));

    console.log("Загруженные данные:", cabinetData.value);
  } catch (error) {
    console.error("Ошибка загрузки данных", error);
  }
};
