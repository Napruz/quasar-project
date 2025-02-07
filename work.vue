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
import { ref } from "vue";

export default {
  setup() {
    const processes = ref([]); // Данные приходят с сервера
    const activeTabs = ref({}); // Храним активные вкладки по индексам

    const loadProcesses = (serverData) => {
      processes.value = serverData;
      serverData.forEach((_, index) => {
        activeTabs.value[index] = "all"; // По умолчанию "Все задачи"
      });
    };

    const pendingTasks = (tasks) => tasks.filter(task => !task.completed);

    return {
      processes,
      activeTabs,
      pendingTasks,
      loadProcesses
    };
  }
};
</script>

<style scoped>
.tab-header {
  display: flex;
  gap: 10px;
}

.tab-title {
  cursor: pointer;
  padding: 5px 10px;
  border-bottom: 2px solid transparent;
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

