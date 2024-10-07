<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <!-- ... существующий код ... -->

      <div class="course-buttons q-mt-sm row justify-between">
        <q-btn
          v-if="category.startCourseLink"
          class="start-button text-white"
          label="Начать прохождение"
          size="15px"
          glossy
          icon="fa-solid fa-play"
          :to="category.startCourseLink"
        />

        <q-btn
          class="cancel-button text-white"
          label="Отменить назначение"
          size="15px"
          glossy
          icon="fa-solid fa-ban"
          @click="cancelCourse"
        />
      </div>

      <div v-if="cancelMessage" class="cancel-message">{{ cancelMessage }}</div> <!-- Сообщение об отмене -->

      <!-- ... существующий код ... -->
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";
import { useCategoryStore } from "../stores/categoryid";

const BACKEND_URL = ;

export default {
  setup() {
    return {
      currentCategoryTitle: ref(""),
    };
  },
  data() {
    return {
      category: {},
      categories: [],
      mainThemes: [],
      cancelMessage: "", // Для отображения сообщения об отмене
    };
  },
  methods: {
    async fetchCategoriesData() {
      // ... существующий код fetchCategoriesData ...
    },
    async fetchCourseData() {
      // ... существующий код fetchCourseData ...
    },
    getBreadcrumbPath() {
      // ... существующий код getBreadcrumbPath ...
    },
    async cancelCourse() {
      try {
        const response = await axios.post(this.category.cancelCourseLink);

        // Проверка ответа сервера
        if (response.data.status === 1) {
          // Успешная отмена
          this.fetchCourseData(); // Обновить данные курса
        } else {
          // Отобразить сообщение об ошибке
          this.cancelMessage = response.data.message;
          setTimeout(() => {
            this.cancelMessage = ""; // Очистить сообщение через некоторое время
          }, 5000);
        }
      } catch (error) {
        console.error("Ошибка при отмене курса:", error);
        this.cancelMessage = "Произошла ошибка при отмене курса.";
        setTimeout(() => {
          this.cancelMessage = ""; // Очистить сообщение об ошибке через некоторое время
        }, 5000);
      }
    },
  },
  mounted() {
    this.fetchCourseData();
  },
};
</script>

<style scoped>
/* Добавьте стили для отображения сообщения об отмене, если необходимо */
.cancel-message {
  color: red;
  margin-top: 10px;
}
</style>

