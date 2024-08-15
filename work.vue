<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <div class="breadcrumbs q-ml-xl q-mt-sm q-gutter-sm row justify-between">
        <q-breadcrumbs class="q-mt-lg g-ml-lg text-primary">
          <q-breadcrumbs-el label="Учебный центр" :to="{ path: '/' }" />
          <q-breadcrumbs-el label="Каталог курсов" :to="{ path: '/content' }" />
          <q-breadcrumbs-el :label="currentCategoryTitle" />
        </q-breadcrumbs>
      </div>

      <div class="container row">
        <div class="column items-start q-pl-sm q-ml-xl q-mt-sm">
          <div class="header-container">
            <p class="text-3 text-weight-regular text-uppercase text-primary text-weight-bold" style="font-size: 25px">
              {{ currentCategoryTitle }}
            </p>
          </div>

          <div class="tab-panel q-gutter-y-md" style="width: 100%">
            <div class="tabs row">
              <q-tabs v-model="tab" class="text-primary q-mb-sm" active-color="secondary" align="justify" narrow-indicator>
                <q-route-tab name="7207783892693892797" label="Профессиональные навыки" @click="reloadPage('7207783892693892797')" />
                <q-route-tab name="7207784069377055908" label="Личная эффективность" @click="reloadPage('7207784069377055908')" />
                <q-route-tab name="7207784147986504834" label="Вводные курсы" @click="reloadPage('7207784147986504834')" />
                <q-route-tab name="7313878051766677160" label="УЭД" @click="reloadPage('7313878051766677160')" />
              </q-tabs>
              <q-btn-toggle
                v-model="isListView"
                class="my-custom-toggle q-ml-lg"
                rounded
                flat
                toggle-color="primary"
                color="white"
                text-color="primary"
                size="md"
                :options="[
                  { icon: 'mdi-format-list-bulleted', value: true },
                  { icon: 'mdi-apps', value: false }
                ]"
              />
            </div>
          </div>

          <!-- Содержимое вкладок -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";


export default {
  setup() {
    return {
      tab: ref(""),
      currentCategoryTitle: ref(""),
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
    toggleListView() {
      this.isListView = !this.isListView;
    },
    toggleBanner(categoryId) {
      this.activeBanner = this.activeBanner === categoryId ? null : categoryId;
    },
    reloadPage(tabId) {
      this.tab = tabId; // Обновляем значение активной вкладки
      const path = `/content-items/${tabId}`;

      // Осуществляем переход на новый маршрут
      this.$router.push(path).then(() => {
        // После успешного перехода перезагружаем страницу
        window.location.reload();
      }).catch(err => {
        console.error("Error navigating to tab:", err);
      });
    },
    async fetchCatalogItemsData() {
      const params = {
        collection_code: "vtbl_quasar_courses_list",
      };
      const categoryId = this.$route.params.id;

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
          this.currentCategoryTitle.value = category.title;
          this.tab = categoryId; // Устанавливаем активную вкладку на основе категории
        } else {
          this.currentCategoryTitle.value = "Категория не найдена";
        }
      } catch (error) {
        console.error("Ошибка при получении данных:", error);
      }
    },
  },
  mounted() {
    this.fetchCatalogItemsData();
  },
};
</script>
