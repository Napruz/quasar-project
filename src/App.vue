<template>
  <div class="comments-container">
    <!-- Форма добавления нового комментария -->
    <q-form @submit.prevent="submitComment" class="comment-form">
      <q-input
        v-model="newComment.text"
        label="Добавить комментарий по выполнению задач на испытательный срок"
        filled
        clearable
        type="textarea"
        autogrow
        :maxlength="1000"
        @input="checkLength"
        @clear="clearComment"
      />
      <div class="char-counter">{{ newComment.text.length }} / 1000</div>

      <q-tooltip v-if="newComment.text.length > 990" class="warning-tooltip">
        Максимальная длина комментария 1000 символов
      </q-tooltip>

      <q-btn class="comment-button" label="Отправить" type="submit" color="secondary" />
    </q-form>

    <!-- Список комментариев -->
    <ul class="comment-list">
      <li v-for="comment in commentListData" :key="comment.id" class="comment-item">
        <div class="comment-user-info">
          <q-avatar size="40px" class="comment-avatar">
            <img :src="comment.person_icon_url" alt="Фото сотрудника" style="object-fit: cover;" />
          </q-avatar>
          <div class="user-meta">
            <div class="user-name">{{ comment.person_fullname }}</div>
            <div class="user-role">{{ comment.person_position }}</div>
            <div class="user-department">{{ comment.person_subdivision }}</div>
          </div>
          <div class="comment-date">
            <div class="comment-phone">{{ comment.date }}</div>
          </div>
        </div>
        <div class="comment-value">
          {{ comment.text }}
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import axios from "axios";

const BACKEND_URL = window.location.origin + 
const BACKEND_POST_URL = window.location.origin + 

export default {
  setup() {
    const commentListData = ref([]);
    const newComment = ref({ text: "" });

    // Загрузка комментариев с сервера
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

        // Удаляем временные комментарии и обновляем список
        commentListData.value = response.data.results.filter(
          (comment) => !comment.isTemporary
        );

        console.log("Обновленный список комментариев:", commentListData.value);
      } catch (error) {
        console.error("Ошибка загрузки комментариев", error);
      }
    };

    // Отправка нового комментария
    const postComment = async (tempId, text) => {
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
            { name: "comment_text", value: text },
          ],
        };

        const formData = new FormData();
        formData.append("action", JSON.stringify(requestBody));

        const queryString = new URLSearchParams({ secid: wtSecId }).toString();

        await axios.post(`${BACKEND_POST_URL}${queryString}`, formData, {
          headers: { "Content-Type": "multipart/form-data" },
        });

        console.log("Комментарий отправлен на сервер");

        // После успешного запроса обновляем комментарии с сервера
        fetchCommentListData();
      } catch (error) {
        console.error("Ошибка при добавлении комментария", error);
      }
    };

    // Добавление комментария в локальный список перед отправкой на сервер
    const submitComment = () => {
      if (!newComment.value.text.trim() || newComment.value.text.length > 1000) return;

      const today = new Date();
      const formattedDate = `${String(today.getDate()).padStart(2, "0")}.${String(today.getMonth() + 1).padStart(2, "0")}.${today.getFullYear()}`;
      const tempId = `temp-${Date.now()}`; // Временный ID

      // Добавляем в список с флагом временного комментария
      commentListData.value.unshift({
        id: tempId,
        person_icon_url: "./images/person-default.png",
        person_fullname: "Тест Г.О.",
        person_position: "Менеджер",
        person_subdivision: "HR",
        date: formattedDate,
        text: newComment.value.text,
        isTemporary: true,
      });

      // Очищаем поле ввода
      newComment.value.text = "";

      // Отправляем на сервер
      postComment(tempId, newComment.value.text);
    };

    // Ограничение длины комментария
    const checkLength = () => {
      if (newComment.value.text.length > 1000) {
        newComment.value.text = newComment.value.text.slice(0, 1000);
      }
    };

    // Очистка поля комментария
    const clearComment = () => {
      newComment.value.text = "";
    };

    onMounted(fetchCommentListData);

    return {
      commentListData,
      newComment,
      submitComment,
      checkLength,
      clearComment,
    };
  },
};
</script>
