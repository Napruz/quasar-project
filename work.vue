<script>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import { useCategoryStore } from "../stores/categoryid";

const BACKEND_URL = window.location.origin + `/pp/Ext5/extjs_json_collection_data.html`;
const BACKEND_POST_URL = window.location.origin + `/lpapi.html?object_id=&doc_id=&`;

export default {
  setup() {
    const activeTab = ref("all");
    const toastVisible = ref(false);
    const toastMessage = ref("");
    const cabinetData = ref([]);
    const materialsData = ref([]);
    const commentListData = ref([]);
    const taskData = ref([]);
    const newComment = ref({
      id: "",
      text: "",
      user: "",
      userPosition: "",
      userAvatar: "",
    });

    const showToast = (message = "Задача сохранена") => {
      toastMessage.value = message;
      toastVisible.value = true;
      setTimeout(() => (toastVisible.value = false), 2000);
    };

    // Отправка комментария
    const postCommentState = async (commentText) => {
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
            { name: "action_name", value: "add_comment" },
            { name: "comment_text", value: commentText },
          ],
        };

        const formData = new FormData();
        formData.append("action", JSON.stringify(requestBody));
        const queryString = new URLSearchParams({ secid: wtSecId }).toString();

        await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
          headers: { "Content-Type": "multipart/form-data" },
        });

        console.log("Комментарий отправлен");
      } catch (error) {
        console.error("Ошибка при добавлении комментария", error);
        showToast("Ошибка при добавлении комментария");
      }
    };

    const checkLength = () => {
      if (newComment.value.text.length > 1000) {
        newComment.value.text = newComment.value.text.slice(0, 1000);
      }
    };

    const clearComment = () => {
      newComment.value.text = "";
    };

    const submitComment = () => {
      if (!newComment.value.text.trim() || newComment.value.text.length > 1000) return;

      const today = new Date();
      const formattedDate = `${String(today.getDate()).padStart(2, "0")}.${String(today.getMonth() + 1).padStart(2, "0")}.${today.getFullYear()}`;

      commentListData.value.unshift({
        id: commentListData.value.length + 1,
        avatar: "#",
        role: "Менеджер",
        name: "Тест Г.О.",
        department: "HR",
        date: formattedDate,
        text: newComment.value.text,
      });

      postCommentState(newComment.value.text);
      newComment.value.text = "";
    };

    const fetchCabinetData = async () => {
      try {
        const params = { collection_code: "vtbl_adaptation_2025", parameters: "data_mode=collaborator_main_data" };
        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());
        cabinetData.value = response.data.results;
      } catch (error) {
        console.error("Ошибка загрузки кабинета", error);
      }
    };

    const fetchMaterialsData = async () => {
      try {
        const params = { collection_code: "vtbl_adaptation_2025", parameters: "data_mode=get_materials" };
        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());
        materialsData.value = response.data.results;
      } catch (error) {
        console.error("Ошибка загрузки материалов", error);
      }
    };

    const fetchCommentListData = async () => {
      try {
        const params = { collection_code: "vtbl_adaptation_2025", parameters: "data_mode=get_comments" };
        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());
        commentListData.value = response.data.results;
      } catch (error) {
        console.error("Ошибка загрузки комментариев", error);
      }
    };

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

        taskData.value = response.data.results;
        console.log("Задача обновлена:", taskData.value);
      } catch (error) {
        console.error("Ошибка обновления задачи", error);
        showToast("Ошибка при обновлении задачи");
      }
    };

    const updateLocalTask = (taskId, newValue) => {
      cabinetData.value.forEach((process) => {
        process.tasks.forEach((task) => {
          if (task.id === taskId) {
            task.completed = newValue;
          }
        });
      });
    };

    const handleToggle = async (newValue, id) => {
      await postTaskState(newValue, id);
      showToast("Задача сохранена");
      updateLocalTask(id, newValue);
    };

    const changeStepperStyle = () => {
      document.querySelectorAll(".q-stepper__tab").forEach((el) => {
        el.style.paddingLeft = "10px";
        el.style.paddingRight = "10px";
      });
    };

    onMounted(() => {
      fetchCabinetData();
      fetchMaterialsData();
      fetchCommentListData();
      changeStepperStyle();
    });

    return {
      activeTab,
      toastVisible,
      toastMessage,
      showToast,
      newComment,
      checkLength,
      clearComment,
      submitComment,
      commentListData,
      cabinetData,
      materialsData,
      taskData,
      handleToggle,
    };
  },
};
</script>
