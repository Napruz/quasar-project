<q-expansion-item
  v-model="expandedTaskProcesses[month.id]"
  expand-separator
  switch-toggle-side
  :label="month.title"
  class="expansion-item-wrapper"
  @update:model-value="(value) => saveExpansionState(month.id, value)"
>

<script>
import { ref, nextTick, onMounted, onUnmounted } from "vue";
import { useQuasar, date } from "quasar";
import axios from "axios";

const BACKEND_URL = window.location.origin + `/pp/Ext5/extjs_json_collection_data.html`;
const BACKEND_POST_URL = window.location.origin + `/lpapi.html?object_id=&doc_id=&`;

export default {
  setup() {
    const expandedTaskProcesses = ref({});

    // Функция для сохранения состояния раскрытия месяцев в sessionStorage
    const saveExpansionState = (monthId, value) => {
      if (typeof window !== "undefined" && window.sessionStorage) {
        sessionStorage.setItem(`expandedMonth_${monthId}`, JSON.stringify(value));
      }
      expandedTaskProcesses.value[monthId] = value;
    };

    // Функция для загрузки состояния раскрытия месяцев из sessionStorage
    const loadExpansionState = (monthId) => {
      if (typeof window !== "undefined" && window.sessionStorage) {
        const storedState = sessionStorage.getItem(`expandedMonth_${monthId}`);
        return storedState ? JSON.parse(storedState) : false;
      }
      return false; // Если sessionStorage не доступен, возвращаем false
    };

    // Загрузка состояния раскрытия для каждого месяца при монтировании
    const fetchCabinetData = async () => {
      try {
        const params = {
          collection_code: "vtbl_adaptation_2025",
          parameters: "data_mode=collaborator_result_task_data",
        };

        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());

        // Обрабатываем данные месяца
        cabinetData.value = response.data.results.map((month) => ({
          ...month,
          tasks: month.tasks.filter((task) => !task.isTemporary),
        }));

        // Восстановление состояния раскрытия месяцев из sessionStorage
        cabinetData.value.forEach((month) => {
          expandedTaskProcesses.value[month.id] = loadExpansionState(month.id);
        });

      } catch (error) {
        console.error("Ошибка загрузки данных", error);
      }
    };

    onMounted(() => {
      fetchCabinetData();
    });

    return {
      expandedTaskProcesses,
      saveExpansionState,
      loadExpansionState,
    };
  },
};
</script>
