<template>
  <div v-for="(process, processIndex) in processes" :key="processIndex" class="process-card">
    <!-- Заголовки табов -->
    <div class="tab-header">
      <p
        class="tab-title"
        :class="{ 'active-tab': activeTabs[processIndex] === 'all' }"
        @click="activeTabs[processIndex] = 'all'"
      >
        Все задачи
      </p>
      <p
        class="tab-title"
        :class="{ 'active-tab': activeTabs[processIndex] === 'pending' }"
        @click="activeTabs[processIndex] = 'pending'"
      >
        Невыполненные задачи
      </p>
    </div>

    <!-- Контент для вкладки "Все задачи" -->
    <div v-if="activeTabs[processIndex] === 'all'" class="tab-content">
      <ul class="task-list">
        <li v-for="(task, taskIndex) in process.tasks" :key="taskIndex" class="task-item">
          <p>{{ task.description }}</p>
          <q-toggle v-model="task.completed" @update:model-value="showToast(task.description)" />
        </li>
      </ul>
    </div>

    <!-- Контент для вкладки "Невыполненные задачи" -->
    <div v-if="activeTabs[processIndex] === 'pending'" class="tab-content">
      <ul class="task-list">
        <li v-for="(task, taskIndex) in pendingTasks(process.tasks)" :key="taskIndex" class="task-item">
          <p>{{ task.description }}</p>
          <q-toggle v-model="task.completed" @update:model-value="showToast(task.description)" />
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  setup() {
    const processes = ref([]); // Данные приходят с сервера
    const activeTabs = ref({}); // Храним активные вкладки по индексам

    // Имитируем загрузку данных с сервера
    const loadProcesses = () => {
      const serverData = [
        {
          id: 1,
          tasks: [
            { description: "Сделать отчёт", completed: false },
            { description: "Проверить почту", completed: true },
            { description: "Запланировать встречу", completed: false }
          ]
        },
        {
          id: 2,
          tasks: [
            { description: "Подготовить презентацию", completed: false },
            { description: "Согласовать бюджет", completed: true }
          ]
        }
      ];
      processes.value = serverData;
      serverData.forEach((_, index) => {
        activeTabs.value[index] = "all"; // По умолчанию показываем "Все задачи"
      });
    };

    const pendingTasks = (tasks) => tasks.filter(task => !task.completed);

    const showToast = (taskDescription) => {
      console.log(`Задача "${taskDescription}" обновлена!`);
    };

    onMounted(() => {
      loadProcesses();
    });

    return {
      processes,
      activeTabs,
      pendingTasks,
      showToast
    };
  }
};
</script>

<style scoped>
.process-card {
  border: 1px solid #ddd;
  padding: 10px;
  margin-bottom: 15px;
  border-radius: 5px;
}

.tab-header {
  display: flex;
  gap: 10px;
}

.tab-title {
  cursor: pointer;
  padding: 5px 10px;
  border-bottom: 2px solid transparent;
  transition: border-color 0.3s ease;
}

.active-tab {
  border-bottom: 2px solid blue;
  font-weight: bold;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 5px 0;
}
</style>
