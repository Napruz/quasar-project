const expandedProcesses = ref({}); // Инициализация пустого объекта

const unWrapProcessList = () => {
  cabinetData.value.forEach((process) => {
    expandedProcesses.value[process.id] = true; // Разворачиваем все процессы
  });
};
