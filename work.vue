<!-- CatalogItems.vue -->
<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <div class="container row">
        <div class="column items-start q-pl-sm q-ml-xl q-mt-sm">
          <div class="tabs row">
            <q-tabs v-model="tab" class="text-primary q-mb-sm wrap" active-color="secondary" align="justify" narrow-indicator>
              <q-route-tab
                v-for="category in categories"
                :key="category.id"
                :name="category.id"
                :label="shortenString(category.title)"
                @click="selectCategory(category.id)" <!-- Передаем ID категории при клике -->
                class="tab-item"
              />
            </q-tabs>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { useCategoryStore } from '@/store/categoryid';

export default {
  data() {
    return {
      tab: null, // Активная вкладка
      categories: [] // Данные категорий
    };
  },
  methods: {
    selectCategory(categoryId) {
      const categoryStore = useCategoryStore();
      categoryStore.setCategoryId(categoryId); // Обновляем ID категории в хранилище
    },
    fetchCategoriesData() {
      // Логика для получения категорий
    },
  },
  mounted() {
    this.fetchCategoriesData(); // Получаем данные при монтировании компонента
  }
};
</script>
