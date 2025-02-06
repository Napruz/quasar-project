<script>
import { ref, computed } from "vue";
import axios from "axios";
import { useCategoryStore } from "../stores/categoryid";



export default {
  setup() {
    const toastVisible = ref(false); // Управляем отображением сообщения
    const toastMessage = ref("");

    const showToast = (message = "Задача сохранена") => {
      toastMessage.value = message;
      toastVisible.value = true;

      // Автоматически скрываем через 2 секунды
      setTimeout(() => {
        toastVisible.value = false;
      }, 2000);
    };

    return {
      currentCategoryTitle: ref(""),
      originURL: computed(() => `${window.location.origin}`),
      toogleValue: ref("Выполнено"),
      step: ref(1),
      showToast,
      toastVisible,
      toastMessage,
    };
  },
};
</script>
<template>
  <div v-if="toastVisible" class="custom-toast">
    {{ toastMessage }}
  </div>

  <q-toggle
    v-model="task.completed"
    :false-value="false"
    :true-value="true"
    :label="task.completed ? 'Выполнено' : 'Не выполнено'"
    color="green"
    unchecked-color="red"
    @update:model-value="showToast"
  />
</template>

<style>
.custom-toast {
  position: fixed;
  top: 20px;
  right: 20px;
  background: green;
  color: white;
  padding: 10px 15px;
  border-radius: 5px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  font-size: 14px;
}
</style>
