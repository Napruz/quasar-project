<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <div class="breadcrumbs q-ml-xl q-mt-sm q-gutter-sm row justify-between">
        <q-breadcrumbs class="q-mt-lg g-ml-lg text-primary">
          <q-breadcrumbs-el label="Учебный центр" :to="{ path: '/' }" />
          <q-breadcrumbs-el label="Каталог курсов" :to="{ path: 'content' }" />
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
              <q-tabs
                v-model="tab"
                class="text-primary q-mb-sm"
                active-color="secondary"
                align="justify"
                narrow-indicator
                @change="changeTab"
              >
                <q-route-tab name="skills" label="Профессиональные навыки" />
                <q-route-tab name="briefings" label="Вводные курсы" />
                <q-route-tab name="efficiency" label="Личная эффективность" />
                <q-route-tab name="ued" label="УЭД" />
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
                  { icon: 'mdi-apps', value: false },
                ]"
              />
            </div>
          </div>

          <div v-if="isListView" class="list">
            <ul class="categories-list">
              <li v-for="category in catalogItems" :key="category.id" class="list-item">
                <div class="row justify-between">
                  <div class="active-hover row">
                    <p class="text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                      <router-link :to="category.path" style="text-decoration: none">
                        {{ category.title }}
                      </router-link>
                    </p>
                    <div class="down-button">
                      <q-btn
                        flat
                        round
                        color="primary"
                        padding="none"
                        size="md"
                        icon="mdi-chevron-down"
                        :class="activeBanner === category.id ? 'transform-button' : 'down-button'"
                        @click="toggleBanner(category.id)"
                      />
                    </div>
                  </div>
                  <div class="description-button">
                    <q-btn
                      glossy
                      size="10px"
                      class="q-ml-xl text-white gradient-button"
                      label="Описание"
                      :to="{ path: category.path + '/:' + category.id }"
                    />
                  </div>
                </div>
                <div v-if="activeBanner === category.id" class="q-mb-md course-catalog-banner-active">
                  <q-banner class="bg-grey-4 banner" rounded>
                    <p class="courses-purpose text-secondary text-h6 q-mb-sm text-uppercase text-bold">
                      Цель курса?
                      <q-icon name="mdi-bullseye-arrow" style="margin-bottom: 3px" />
                    </p>
                    <p class="courses-purpose-description text-primary q-mb-md ellipsis-2-lines" style="max-width: 700px">
                      {{ category.target }}
                    </p>
                    <p class="courses-time text-secondary text-h6 q-mb-sm text-uppercase text-bold">
                      Время прохождения
                      <q-icon name="schedule" style="margin-bottom: 3px" />
                    </p>
                    <p class="courses-time-item text-primary q-mb-sm text-lowercase" style="max-width: 700px">
                      {{ category.time }} минут
                    </p>
                  </q-banner>
                </div>
              </li>
            </ul>
          </div>

          <div v-else class="list-card">
            <ul class="categories-list categories-card">
              <li v-for="category in catalogItems" :key="category.id" class="card-item">
                <div class="card-container q-py-md row q-gutter-md row wrap" style="margin-left: 1px">
                  <router-link to="course" style="text-decoration: none">
                    <q-card class="my-card col-3 shadow-3 position-relative" bordered>
                      <q-card-section horizontal>
                        <q-card-section class="q-pt-xs">
                          <div class="text-h5 q-mt-sm q-mb-xs text-weight-bold ellipsis-1-lines" style="font-size: medium">
                            {{ category.title }}
                          </div>
                          <div class="text-subtitle1 q-mt-sm q-mb-xs" style="font-size: small; font-weight: 500">
                            Цель курса?
                          </div>
                          <div class="text-caption text-grey ellipsis-2-lines">
                            {{ category.target }}
                          </div>
                        </q-card-section>
                      </q-card-section>

                      <div class="course-time">
                        <q-icon flat size="large" name="schedule" color="secondary" style="margin-top: 1px" class="time-icon" />
                        <div flat class="q-ml-sm card-time">
                          {{ category.bannerTime }} мин
                        </div>
                      </div>
                    </q-card>
                  </router-link>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

const BACKEND_URL = "https://als-wt/pp/Ext5/extjs_json_collection_data.html";

export default {
  setup() {
    return {
      tab: ref("skills"),
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

    async fetchCatalogItemsData() {
      const params = {
        collection_code: "vtbl_quasar_courses_list",
      };
      const categoryId = this.$route.params.id.substring(1);

      try {
        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());

        this.catalogItems = response.data.results.filter((item) => item.parentCategory.includes(categoryId));

        // Поиск названия текущей категории на основе ID категории
        const category = response.data.results.find((item) => item.parentCategory.includes(categoryId));
        if (category) {
          this.currentCategoryTitle = category.title;
        } else {
          this.currentCategoryTitle = "Категория не найдена";
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
