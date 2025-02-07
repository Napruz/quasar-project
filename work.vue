<q-tabs v-model="selectedTabs[processIndex]" dense class="text-grey">
  <q-tab name="all" label="Все задачи" />
  <q-tab name="pending" label="Невыполненные задачи" />
</q-tabs>

<q-tab-panels v-model="selectedTabs[processIndex]" animated>
  <q-tab-panel name="all">
    <ul class="stages-list">
      <li v-for="(task, taskIndex) in process.tasks" :key="taskIndex" class="stages-item">
        <p>{{ task.description }}</p>
        <q-toggle v-model="task.completed" @update:model-value="showToast(task.description)" />
      </li>
    </ul>
  </q-tab-panel>

  <q-tab-panel name="pending">
    <ul class="stages-list">
      <li v-for="(task, taskIndex) in pendingTasks(process.tasks)" :key="taskIndex" class="stages-item">
        <p>{{ task.description }}</p>
        <q-toggle v-model="task.completed" @update:model-value="showToast(task.description)" />
      </li>
    </ul>
  </q-tab-panel>
</q-tab-panels>
setup() {
  const processes = ref([]); // Данные приходят с сервера
  const selectedTabs = ref({}); // Здесь храним выбранные вкладки для каждого процесса

  // Функция, вызываемая при получении данных с сервера
  const loadProcesses = (serverData) => {
    processes.value = serverData;
    // Инициализируем selectedTabs для каждого процесса
    serverData.forEach((_, index) => {
      selectedTabs.value[index] = "all"; // По умолчанию вкладка "Все задачи"
    });
  };

  const pendingTasks = (tasks) => tasks.filter(task => !task.completed);

  return {
    processes,
    selectedTabs,
    pendingTasks,
    loadProcesses
  };
}

