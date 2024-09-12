// store/categoryid.js
import { defineStore } from 'pinia';

export const useCategoryStore = defineStore('category', {
  state: () => ({
    currentCategoryId: null, // Здесь будет храниться ID текущей категории
  }),

  actions: {
    setCategoryId(categoryId) {
      this.currentCategoryId = categoryId; // Обновляем ID категории
    },
  },
});

