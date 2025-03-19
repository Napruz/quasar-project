<script>
import { ref, onMounted, onUnmounted, nextTick } from "vue";
import axios from "axios";

export default {
  setup() {
    const activeTab = ref("all");
    const toastVisible = ref(false);
    const toastMessage = ref("");
    const cabinetData = ref([]);
    const usersData = ref([]);
    const taskData = ref([]);
    const expandedProcesses = ref({}); // Хранение состояний
    const allExpanded = ref(false);
    let observer;

    // Загружаем состояния процессов из sessionStorage
    const loadExpandedStates = () => {
      const savedStates = sessionStorage.getItem("expandedProcesses");
      if (savedStates) {
        expandedProcesses.value = JSON.parse(savedStates);
      }
    };

    // Сохранение состояний процессов в sessionStorage
    const saveExpandedStates = () => {
      sessionStorage.setItem(
        "expandedProcesses",
        JSON.stringify(expandedProcesses.value)
      );
    };

    // При инициализации загружаем состояние allExpanded
    const savedAllExpanded = sessionStorage.getItem("allExpanded");
    if (savedAllExpanded !== null) {
      allExpanded.value = JSON.parse(savedAllExpanded);
    }

    const toggleProcessList = () => {
      const newState = !allExpanded.value;
      cabinetData.value.forEach((process) => {
        expandedProcesses.value[process.id] = newState;
      });
      allExpanded.value = newState;
      sessionStorage.setItem("allExpanded", JSON.stringify(allExpanded.value));
      saveExpandedStates();
    };

    const toggleProcess = (processId) => {
      expandedProcesses.value[processId] = !expandedProcesses.value[processId];
      saveExpandedStates();
    };

    const fetchCabinetData = async () => {
      try {
        const params = {
          collection_code: "vtbl_adaptation_2025",
          parameters: "data_mode=collaborator_main_data",
        };
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );
        cabinetData.value = response.data.results;
        nextTick(() => {
          cabinetData.value.forEach((process) => {
            if (!(process.id in expandedProcesses.value)) {
              expandedProcesses.value[process.id] = allExpanded.value;
            }
          });
          saveExpandedStates();
        });
      } catch (error) {
        console.error("Ошибка загрузки кабинета", error);
      }
    };

    onMounted(async () => {
      loadExpandedStates();
      await fetchCabinetData();
    });

    onUnmounted(() => {
      observer?.disconnect();
    });

    return {
      activeTab,
      cabinetData,
      usersData,
      taskData,
      expandedProcesses,
      toggleProcessList,
      toggleProcess,
      allExpanded,
    };
  },
};
</script>
