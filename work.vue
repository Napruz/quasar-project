<!-- CatalogPage.vue -->
<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <div class="new-card">
        <q-card class="my-card">
          <q-img class="card-header-bg-image" src="images/card-header-panel.png">
            <div v-if="category.state === 'inProgress'" class="course-state in-progress">
              <q-img src="icons/in-progress-state.svg" />
            </div>
          </q-img>
        </q-card>
      </div>
    </div>
  </div>
</template>

<script>
import { useCategoryStore } from '@/store/categoryid';
import axios from 'axios';

export default {
  data() {
    return {
      category: {}, // Данные курса
      courses: [] // Курсы в категории
    };
  },
  methods: {
    fetchCourseData() {
      const categoryStore = useCategoryStore(); // Получаем доступ к хранилищу
      const categoryId = categoryStore.currentCategoryId; // Получаем ID категории

      // Выполняем запрос с использованием полученного ID
      const params = {
        collection_code: 'vtbl_quasar_courses_list',
        parameters: `category_id=${categoryId}`,
      };

      axios.post('https://als-wt/pp/Ext5/extjs_json_collection_data.html', new URLSearchParams(params).toString())
        .then(response => {
          this.courses = response.data.results;
        })
        .catch(error => {
          console.error('Ошибка загрузки данных курса:', error);
        });
    }
  },
  mounted() {
    this.fetchCourseData(); // Получаем данные при монтировании
  }
};
</script>
