<template>
  <router-view />
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'App'
})
</script>


<script>
import { ref, computed, onMounted } from "vue";
import { useQuasar, date } from "quasar";
import axios from "axios";

const BACKEND_URL = window.location.origin + "/backend";
const BACKEND_POST_URL = window.location.origin + "/post-backend";
const wtSecId = "your-sec-id"; // Укажите реальное значение

export default {
  setup() {
    const toastVisible = ref(false);
    const toastMessage = ref("");
    const newTaskText = ref("");
    const newTaskDate = ref("");
    const selectedMonth = ref("1");
    const isModalOpen = ref(false);
    const cabinetData = ref([]);
    const courseButtons = ref([]);

    // ✅ Показ уведомлений
    const showToast = (message = "Задача сохранена") => {
      toastMessage.value = message;
      toastVisible.value = true;
      setTimeout(() => {
        toastVisible.value = false;
      }, 2000);
    };

    // ✅ Открытие/закрытие модального окна
    const openModal = () => {
      isModalOpen.value = true;
    };

    const closeModal = () => {
      isModalOpen.value = false;
    };

    const clearModal = () => {
      newTaskText.value = "";
      newTaskDate.value = "";
      isModalOpen.value = false;
    };

    // ✅ Загрузка данных с сервера
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

        cabinetData.value = response.data.results;
        console.log("Загруженные данные:", cabinetData.value);
      } catch (error) {
        console.error("Ошибка загрузки данных", error);
      }
    };

    // ✅ Обновление локального состояния задач
    const updateLocalTask = (taskId, newValue) => {
      cabinetData.value.forEach((process) => {
        process.tasks.forEach((task) => {
          if (task.id === taskId) {
            task.completed = newValue;
          }
        });
      });
    };

    // ✅ Отправка изменения статуса задачи
    const postTaskState = async (newValue, taskId) => {
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
            { name: "action_name", value: "save_task" },
            { name: "task_id", value: taskId },
            { name: "task_status", value: Number(newValue) },
            { name: "task_comment", value: "" },
          ],
        };

        const formData = new FormData();
        formData.append("action", JSON.stringify(requestBody));

        const queryString = new URLSearchParams({ secid: wtSecId }).toString();
        const response = await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
          headers: { "Content-Type": "multipart/form-data" },
        });

        console.log("Ответ сервера:", response.data.results);
      } catch (error) {
        console.error("Ошибка при обновлении состояния задачи", error);
        showToast("Ошибка при обновлении задачи");
      }
    };

    // ✅ Обработчик переключателя
    const handleToggle = async (newValue, id) => {
      await postTaskState(newValue, id);
      updateLocalTask(id, newValue);
      showToast("Задача сохранена");
    };

    // ✅ Добавление новой задачи
    const updateLocalNewTaskList = (taskMonth, taskText, taskDate) => {
      const month = cabinetData.value.find((m) => m.id === taskMonth);
      if (month) {
        month.tasks.push({
          id: Date.now(),
          name: taskText,
          date: taskDate,
          completed: false,
        });
      }
    };

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
            { name: "action_name", value: `"add_${taskMonth}_mounth_task"` },
            { name: "task_name", value: taskText },
            { name: "task_date", value: taskDate },
            { name: "task_comment", value: "1" },
          ],
        };

        const formData = new FormData();
        formData.append("action", JSON.stringify(requestBody));

        const queryString = new URLSearchParams({ secid: wtSecId }).toString();
        const response = await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
          headers: { "Content-Type": "multipart/form-data" },
        });

        console.log("Ответ сервера:", response.data.results);
      } catch (error) {
        console.error("Ошибка при добавлении задачи", error);
        showToast("Ошибка при добавлении задачи");
      }
    };

    const addNewTask = async (taskMonth, taskText, taskDate) => {
      await postNewTask(taskMonth, taskText, taskDate);
      updateLocalNewTaskList(taskMonth, taskText, taskDate);
      showToast("Задача добавлена");
    };

    // ✅ Сохранение задачи
    const saveTask = (monthString) => {
  const monthMap = {
    "Первый месяц работы": "1",
    "Второй месяц работы": "2",
    "Третий месяц работы": "3",
  };

  if (monthMap[monthString]) {
    selectedMonth.value = monthMap[monthString];
  } else {
    showToast("Некорректное название месяца");
    return;
  }

  if (!newTaskText.value.trim() || !newTaskDate.value.trim()) {
    showToast("Заполните все поля");
    return;
  }

  addNewTask(selectedMonth.value, newTaskText.value, newTaskDate.value);
  closeModal();
  clearModal();
};


    // ✅ Автоматический вызов при загрузке
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
      courseButtons,
      handleToggle,
    };
  },
};
</script>
