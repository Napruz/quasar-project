<script>
import { ref, nextTick, onMounted, onUnmounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const cabinetData = ref([]);
    const expandedTaskProcesses = ref({});
    const allTaskExpanded = ref(false);
    let observer;

    const fetchCabinetData = async () => {
      try {
        const params = {
          collection_code: "vtbl_adaptation_2025",
          parameters: "data_mode=collaborator_result_task_data",
        };

        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        cabinetData.value = response.data.results.map((month) => ({
          ...month,
          tasks: month.tasks.filter((task) => !task.isTemporary),
        }));
      } catch (error) {
        console.error("Ошибка загрузки данных", error);
      }
    };

    const toggleProcessList = () => {
      if (allTaskExpanded.value) {
        cabinetData.value.forEach((month) => {
          expandedTaskProcesses.value[month.id] = false;
          sessionStorage.setItem(`expandedMonth_${month.id}`, JSON.stringify(false));
        });
      } else {
        cabinetData.value.forEach((month) => {
          expandedTaskProcesses.value[month.id] = true;
          sessionStorage.setItem(`expandedMonth_${month.id}`, JSON.stringify(true));
        });
      }
      allTaskExpanded.value = !allTaskExpanded.value;
      sessionStorage.setItem("allTaskExpanded", JSON.stringify(allTaskExpanded.value));
    };

    onMounted(async () => {
      await fetchCabinetData();

      // Восстанавливаем состояние раскрытых месяцев из sessionStorage
      cabinetData.value.forEach((month) => {
        expandedTaskProcesses.value[month.id] = JSON.parse(sessionStorage.getItem(`expandedMonth_${month.id}`)) || false;
      });

      observer = new MutationObserver(async (mutationsList) => {
        await nextTick();
        mutationsList.forEach(() => {
          changeStyles();
          changeStepperStyle();
          changeFontSizeStyle();
          changeAnotherFontSizeStyle();
          changeLeftLineStyle();
          changeQItemLabelStyle();
          changeQItemLabelSize();
          stepperTab();
        });
      });

      observer.observe(document.querySelector(".content-container"), {
        childList: true,
        subtree: true,
        attributes: true,
      });
    });

    onUnmounted(() => {
      observer.disconnect();
    });

    return {
      cabinetData,
      expandedTaskProcesses,
      allTaskExpanded,
      toggleProcessList,
    };
  },
};
</script>
