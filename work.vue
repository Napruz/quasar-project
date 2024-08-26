<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <!-- Хлебные крошки -->
      <div class="breadcrumbs-container q-mt-sm q-gutter-sm row justify-between">
        <q-breadcrumbs class="q-mt-lg g-ml-lg text-primary">
          <q-breadcrumbs-el label="Учебный центр" :to="{ path: '/' }" />
          <q-breadcrumbs-el label="Каталог курсов" :to="{ path: '/content' }" />
          <q-breadcrumbs-el
            :label="currentCategoryTitle"
            :to="{ path: getBreadcrumbPath() }"
          />
          <q-breadcrumbs-el :label="category.title" />
        </q-breadcrumbs>
      </div>

      <!-- Карточка курса -->
      <div class="new-card">
        <q-card class="my-card">
          <!-- Оставшийся код карточки курса -->
        </q-card>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

const BACKEND_URL = "https://als-wt/pp/Ext5/extjs_json_collection_data.html";

// Предопределенные категории
const CATEGORIES = [
  { id: "7207783892693892797", title: "Профессиональные навыки" },
  { id: "7207784069377055908", title: "Личная эффективность и развитие" },
  { id: "7207784147986504834", title: "Вводные курсы (инструктажи)" },
  { id: "7313878051766677160", title: "Управление эффективностью деятельности (УЭД)" },
];

export default {
  setup() {
    return {
      currentCategoryTitle: ref(""),
    };
  },
  data() {
    return {
      category: {},
    };
  },
  methods: {
    async fetchCourseData() {
      try {
        const params = {
          collection_code: "vtbl_quasar_course_data",
          parameters: "course_id=" + this.$route.params.id.substring(1),
        };

        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        const courseData = response.data.results[0];
        this.category = courseData;

        // Ищем категорию по parentCategory
        const categoryId = courseData.parentCategory[0];
        const categoryName = CATEGORIES.find((cat) => cat.id === categoryId);

        if (categoryName) {
          this.currentCategoryTitle = categoryName.title;
        } else {
          this.currentCategoryTitle = "Список курсов";
        }
      } catch (error) {
        console.error("Ошибка загрузки курса", error);
      }
    },

    // Функция для получения правильного пути
    getBreadcrumbPath() {
      const matchedCategory = CATEGORIES.find(
        (cat) => cat.title === this.currentCategoryTitle
      );

      // Если нашли совпадение, возвращаем путь с правильным id
      if (matchedCategory) {
        return `/content-items/${matchedCategory.id}`;
      }

      // Если нет совпадений, возвращаем путь с id первой категории
      return `/content-items/${CATEGORIES[0].id}`;
    },
  },

  mounted() {
    this.fetchCourseData();
  },
};
</script>

