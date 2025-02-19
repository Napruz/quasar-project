const updateLocalNewTaskList = (taskMonth, taskText, taskDate) => {
  const monthIndex = cabinetData.value.findIndex((m) => m.title === taskMonth);

  if (monthIndex !== -1) {
    const updatedMonth = { ...cabinetData.value[monthIndex] }; // Копия объекта месяца
    updatedMonth.tasks = [...updatedMonth.tasks]; // Копия массива задач

    updatedMonth.tasks.push({
      id: `temp-${Date.now()}`, // Временный ID
      taskName: taskText,
      dueDate: taskDate,
      completed: false,
      canChange: true,
      completedMentor: false,
    });

    // Обновляем cabinetData с новой копией
    cabinetData.value = [
      ...cabinetData.value.slice(0, monthIndex),
      updatedMonth,
      ...cabinetData.value.slice(monthIndex + 1),
    ];
  } else {
    console.warn(`Не найден месяц: ${taskMonth}`);
  }
};
