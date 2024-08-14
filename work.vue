<template>
  <q-tabs
    v-model="tab"
    class="text-primary q-mb-sm"
    active-color="secondary"
    align="justify"
    narrow-indicator
  >
    <q-route-tab
      name="7207783892693892797"
      label="Профессиональные навыки"
      :to="{ path: `/content-items/7207783892693892797` }"
    />
    <q-route-tab
      name="7207784069377055908"
      label="Личная эффективность"
      :to="{ path: `/content-items/7207784069377055908` }"
    />
    <q-route-tab
      name="7207784147986504834"
      label="Вводные курсы"
      :to="{ path: `/content-items/7207784147986504834` }"
    />
    <q-route-tab
      name="7313878051766677160"
      label="УЭД"
      :to="{ path: `/content-items/7313878051766677160` }"
    />
  </q-tabs>
</template>

<script>
import { ref, watch } from "vue";
import axios from "axios";

const CATEGORIES = [
  { id: "7207783892693892797", title: "Профессиональные навыки" },
  { id: "7207784069377055908", title: "Личная эффективность и развитие" },
  { id: "7207784147986504834", title: "Вводные курсы (инструктажи)" },
  { id: "7313878051766677160", title: "Управление эффективностью деятельности (УЭД)" },
];

export default {
  setup() {
    const tab = ref("");
    const currentCategoryTitle = ref("");

    return {
      tab,
      currentCategoryTitle,
    };
  },
  data() {
    return {
      isListView: true,
      catalogItems: [],
      activeBanner: null,
    };
  },
  methods: {
    async fetchCatalogItemsData() {
      const params = {
        collection_code: "vtbl_quasar_courses_list",
      };
      const categoryId = this.$route.params.id.substring(1);

      try {
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        this.catalogItems = response.data.results.filter((item) => {
          return item.parentCategory.includes(categoryId);
        });

        const category = CATEGORIES.find((cat) => cat.id === categoryId);

        if (category) {
          this.currentCategoryTitle = category.title;
          this.tab = category.id; // Устанавливаем активную вкладку
        } else {
          this.currentCategoryTitle = "Категория не найдена";
          this.tab = CATEGORIES[0].id; // Устанавливаем первую категорию, если не найдено
        }
      } catch (error) {
        console.error("Ошибка при получении данных:", error);
      }
    },
  },
  watch: {
    // Следим за изменением маршрута и обновляем активную вкладку
    '$route.params.id'(newId) {
      this.tab = newId;
      this.fetchCatalogItemsData();
    },
    // Следим за изменением таба и выполняем переход на новый маршрут
    tab(newTab) {
      if (newTab !== this.$route.params.id.substring(1)) {
        this.$router.push({ path: `/content-items/${newTab}` });
      }
    }
  },
  mounted() {
    this.fetchCatalogItemsData();
  },
};
</script>
