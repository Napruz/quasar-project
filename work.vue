<template>
  <ul class="stages-list">
    <li
      v-for="(task, index) in tasks"
      :key="index"
      class="stages-item"
    >
      <p class="process-description">{{ task.text }}</p>

      <q-toggle
        :false-value="`Не выполнено`"
        :label="toggleValues[task.id] ? `Выполнено` : `Не выполнено`"
        :true-value="`Выполнено`"
        v-model="toggleValues[task.id]"
        color="green"
        unchecked-color="red"
      />
    </li>
  </ul>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    const tasks = ref([
      { id: 1, text: "Я принял участие во встрече с руководителем" },
      { id: 2, text: "Я познакомился с командой" },
      { id: 3, text: "Получил задачи на испытательный срок" },
    ]);

    const toggleValues = ref({});

    return { tasks, toggleValues };
  },
};
</script>
