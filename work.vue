import { ref } from "vue";
import { useQuasar } from "quasar";

export default {
  setup() {
    const $q = useQuasar(); // Инициализируем Quasar

    const showToast = () => {
      $q.notify({
        message: "Задача сохранена",
        color: "green",
        position: "top-right",
        timeout: 2000,
      });
    };

    return {
      showToast,
    };
  },
};
<q-toggle
  v-model="task.completed"
  :false-value="false"
  :true-value="true"
  :label="task.completed ? 'Выполнено' : 'Не выполнено'"
  color="green"
  unchecked-color="red"
  @update:model-value="showToast"
/>
