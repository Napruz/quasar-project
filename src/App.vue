<script>
import { ref, onMounted } from "vue";
import axios from "axios";

const BACKEND_URL = window.location.origin + `/pp/Ext5/extjs_json_collection_data.html`;
const BACKEND_POST_URL = window.location.origin + `/lpapi.html?object_id=&doc_id=&`;

export default {
  setup() {
    const toastVisible = ref(false);
    const toastMessage = ref("");
    const newTaskText = ref("");
    const newTaskDate = ref("");
    const selectedMonth = ref(null);
    const isModalOpen = ref(false);
    const cabinetData = ref([]);

    // Соответствие месяцев (чтобы избежать ошибок)
    const monthMap = {
      "Первый месяц работы": "1",
      "Второй месяц работы": "2",
      "Третий месяц работы": "3",
    };

    // Показ уведомлений
    const showToast = (message = "Задача сохранена") => {
      toastMessage.value = message;
      toastVisible.value = true;
      setTimeout(() => {
        toastVisible.value = false;
      }, 2000);
    };

    // Открытие модального окна
    const openModal = (monthTitle) => {
      selectedMonth.value = monthTitle;
      isModalOpen.value = true;
    };

    const closeModal = () => {
      isModalOpen.value = false;
    };

    // Очистка формы
    const clearModal = () => {
      newTaskText.value = "";
      newTaskDate.value = "";
      selectedMonth.value = null;
      isModalOpen.value = false;
    };

    // Загрузка данных с сервера
    const fetchCabinetData = async () => {
      try {
        const params = {
          collection_code: "vtbl_adaptation_2025",
          parameters: "data_mode=collaborator_result_task_data",
        };

        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());
        cabinetData.value = response.data.results;
        console.log("Загруженные данные:", cabinetData.value);
      } catch (error) {
        console.error("Ошибка загрузки данных", error);
      }
    };

    // Добавление новой задачи в локальный список
    const updateLocalNewTaskList = (taskMonth, taskText, taskDate) => {
      let monthData = cabinetData.value.find((m) => m.title === taskMonth);

      if (!monthData) {
        console.warn(`Месяц "${taskMonth}" не найден. Создаем новый.`);
        
        // Если месяца нет, создаем его
        monthData = {
          title: taskMonth,
          tasks: [],
        };
        cabinetData.value.push(monthData);
      }

      monthData.tasks.push({
        id: `temp-${Date.now()}`, // Временный ID
        taskName: taskText,
        dueDate: taskDate,
        completed: false,
        canChange: true,
        completedMentor: false,
      });

      // Глубокая реактивность
      cabinetData.value = [...cabinetData.value];

      // Очищаем модалку после успешного добавления
      clearModal();
    };

    // Отправка новой задачи на сервер
    const postNewTask = async (taskMonth, taskText, taskDate) => {
      try {
        const requestBody = {
          action: "eval_action",
          remote_action_id: "7472730402329554549",
          wvars: [
            { name: "_object_id", value: "" },
            { name: "_secid", value: wtSecId },
            { name: "user_id", value: "" },
            { name: "adaptation_id", value: "" },
            { name: "role", value: "collaborator" },
            { name: "action_name", value: `add_${taskMonth}_mounth_task` },
            { name: "task_name", value: taskText },
            { name: "task_date", value: taskDate },
            { name: "task_comment", value: "" },
          ],
        };

        const formData = new FormData();
        formData.append("action", JSON.stringify(requestBody));

        const queryString = new URLSearchParams({ secid: wtSecId }).toString();
        await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
          headers: { "Content-Type": "multipart/form-data" },
        });

        updateLocalNewTaskList(taskMonth, taskText, taskDate);
        showToast("Задача добавлена");
      } catch (error) {
        console.error("Ошибка при добавлении задачи", error);
        showToast("Ошибка при добавлении задачи");
      }
    };

    // Добавление новой задачи
    const addNewTask = async (taskMonth, taskText, taskDate) => {
      await postNewTask(taskMonth, taskText, taskDate);
    };

    // Сохранение задачи
    const saveTask = () => {
      if (!selectedMonth.value) {
        showToast("Ошибка: не выбран месяц");
        return;
      }

      const mappedMonth = monthMap[selectedMonth.value];
      if (!mappedMonth) {
        showToast("Некорректное название месяца");
        return;
      }

      if (!newTaskText.value.trim() || !newTaskDate.value.trim()) {
        showToast("Заполните все поля");
        return;
      }

      addNewTask(mappedMonth, newTaskText.value, newTaskDate.value);
    };

    // Загрузка данных при старте
    onMounted(() => {
      fetchCabinetData();
    });

    return {
      toastVisible,
      toastMessage,
      showToast,
      openModal,
      closeModal,
      clearModal,
      saveTask,
      newTaskText,
      newTaskDate,
      selectedMonth,
      isModalOpen,
      cabinetData,
    };
  },
};
</script>
