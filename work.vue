<script>

import { ref, computed, onMounted, nextTick } from "vue";

import axios from "axios";

 

const BACKEND_URL =

  window.location.origin + `/pp/Ext5/extjs_json_collection_data.html`;

const BACKEND_POST_URL =

  window.location.origin + `/lpapi.html?object_id=&doc_id=&`;

 

export default {

  setup() {

    const activeTab = ref("all");

    const toastVisible = ref(false);

    const toastMessage = ref("");

    const cabinetData = ref([]);

    const usersData = ref([]);

    const materialsData = ref([]);

    const commentListData = ref([]);

    const coworkersData = ref([]);

    const isCoworkersModalOpen = ref(false);

    const taskData = ref([]);

    const newComment = ref({

      id: "",

      text: "",

      user: "",

      userPosition: "",

      userAvatar: "",

    });

 

    const printPage = async () => {

      await nextTick() // Дожидаемся завершения обновлений DOM

 

      const drawer = document.querySelector('.q-drawer');

      const qPageContainer = document.querySelector('.q-page-container');

 

        if (drawer) {

          drawer.style.display = 'none';

          qPageContainer.style.paddingLeft = 0;

        }

 

        setTimeout(() => {

          window.print()

 

          // Вернем меню после печати

          setTimeout(() => {

            if (drawer) {

              drawer.style.display = '';

              qPageContainer.style.paddingLeft = '360px';

            }

          }, 500)

        }, 300) // Подбираем задержку под твою страницу

    }

 

    const showToast = (message = "Задача сохранена") => {

      toastMessage.value = message;

      toastVisible.value = true;

      setTimeout(() => (toastVisible.value = false), 2000);

    };

 

    const openCoworkersModal = async () => {

      isCoworkersModalOpen.value = true;

      await fetchCoworkers();

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

      if (!newComment.value.text.trim() || newComment.value.text.length > 1000)

        return;

 

      const now = new Date();

      const formattedDate = `${String(now.getDate()).padStart(2, "0")}.${String(

        now.getMonth() + 1

      ).padStart(2, "0")}.${now.getFullYear()} ${String(

        now.getHours()

      ).padStart(2, "0")}:${String(now.getMinutes()).padStart(2, "0")}:${String(

        now.getSeconds()

      ).padStart(2, "0")}`;

 

      commentListData.value.unshift({

        id: usersData.value.person.person_id,

        person_icon_url: usersData.value.person.person_icon_url,

        person_position: usersData.value.person.person_position,

        person_fullname: usersData.value.person.person_fullname,

        person_subdivision: usersData.value.person.person_subdivision,

        date: formattedDate,

        text: newComment.value.text,

      });

 

      postCommentState(newComment.value.text);

      newComment.value.text = "";

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

      } catch (error) {

        console.error("Ошибка загрузки кабинета", error);

      }

    };

 

    const fetchUsersData = async () => {

      try {

        const params = {

          collection_code: "vtbl_adaptation_2025",

          parameters: "data_mode=get_adaptation_info",

        };

        const response = await axios.post(

          BACKEND_URL,

          new URLSearchParams(params).toString()

        );

        usersData.value = response.data.results[0];

        console.log(usersData.value.person);

      } catch (error) {

        console.error("Ошибка загрузки кабинета", error);

      }

    };

 

    const fetchCoworkers = async () => {

      try {

        const params = {

          collection_code: "vtbl_adaptation_2025",

          parameters: "data_mode=meet_collaborator",

        };

        const response = await axios.post(

          BACKEND_URL,

          new URLSearchParams(params).toString()

        );

 

        coworkersData.value = response.data.results;

        console.log(coworkersData.value);

      } catch (error) {

        console.error("Ошибка загрузки кабинета", error);

      }

    };

 

    const fetchMaterialsData = async () => {

      try {

        const params = {

          collection_code: "vtbl_adaptation_2025",

          parameters: "data_mode=get_materials",

        };

        const response = await axios.post(

          BACKEND_URL,

          new URLSearchParams(params).toString()

        );

        materialsData.value = response.data.results;

      } catch (error) {

        console.error("Ошибка загрузки материалов", error);

      }

    };

 

    const fetchCommentListData = async () => {

      try {

        const params = {

          collection_code: "vtbl_adaptation_2025",

          parameters: "data_mode=get_comments",

        };

        const response = await axios.post(

          BACKEND_URL,

          new URLSearchParams(params).toString()

        );

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

 

        const response = await axios.post(

          `${BACKEND_POST_URL}${queryString}`,

          formData,

          {

            headers: { "Content-Type": "multipart/form-data" },

          }

        );

 

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

        el.style.paddingLeft = "5px";

        el.style.paddingRight = "5px";

      });

 

    };

 

    const changeFontSizeStyle = () => {

      document.querySelectorAll(".q-stepper__title").forEach((el) => {

        el.style.fontSize = "14px";

      })

    };

 

    const changeAnotherFontSizeStyle = () => {

      document.querySelectorAll(".q-stepper__caption").forEach((el) => {

        el.style.fontSize = "12px";

      })

    };

 

    onMounted(() => {

      fetchCabinetData();

      fetchMaterialsData();

      fetchCommentListData();

      fetchUsersData();

      fetchCoworkers();

 

      const observer = new MutationObserver(() => {

        changeStepperStyle();

        changeFontSizeStyle();

        changeAnotherFontSizeStyle();

      });

 

      observer.observe(document.body, {childList: true, subtree: true});

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

      usersData,

      materialsData,

      taskData,

      handleToggle,

      isCoworkersModalOpen,

      coworkersData,

      openCoworkersModal,

      printPage

    };

  },

};

</script>
