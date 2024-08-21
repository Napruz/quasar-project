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
            <p
              class="text-3 text-weight-regular text-uppercase text-primary text-weight-bold"
              style="font-size: 25px"
            >
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
              >
                <q-route-tab
                  name="7207783892693892797"
                  label="Профессиональные навыки"
                  @click="reloadPage(':7207783892693892797')"
                />
                <q-route-tab
                  name="7207784069377055908"
                  label="Личная эффективность"
                  @click="reloadPage(':7207784069377055908')"
                />
                <q-route-tab
                  name="7207784147986504834"
                  label="Вводные курсы"
                  @click="reloadPage(':7207784147986504834')"
                />
                <q-route-tab
                  name="7313878051766677160"
                  label="УЭД"
                  @click="reloadPage(':7313878051766677160')"
                />
              </q-tabs>

              <q-btn-toggle
                v-model="currentView"
                class="my-custom-toggle q-ml-lg"
                unelevated
                toggle-color="primary"
                color="white"
                text-color="primary"
                size="md"
                :options="[
                  { icon: 'mdi-format-list-bulleted', value: 'isListView' },
                  { icon: 'mdi-format-list-text', value: 'isFullListView' },
                  { icon: 'mdi-apps', value: 'listCardView' }
                ]"
                @click="toggleListView(currentView)"
              />
            </div>
          </div>

          <div v-if="currentView === 'isListView'" class="list">
            <ul class="categories-list">
              <li
                v-for="category in catalogItems"
                :key="category.id"
                class="list-item"
              >
                <div class="row justify-between">
                  <div class="active-hover row">
                    <p
                      class="list-text text-body1 text-primary text-weight-bold q-mr-sm underline-text"
                    >
                      <q-tooltip anchor="bottom middle" self="bottom middle">
                        Перейти на страницу курса
                      </q-tooltip>
                      <router-link
                        class="list-item-link"
                        :to="{ path: category.path+/:+ category.id }"
                      >
                        {{ category.title }}
                      </router-link>
                    </p>
                    <div class="down-button">
                      <q-btn
                        unelevated
                        round
                        color="grey-4"
                        text-color="grey-9"
                        padding="none"
                        size="md"
                        icon="mdi-chevron-down"
                        :class="activeBanner === category.id ? 'transform-button' : 'down-button'"
                        @click="toggleBanner(category.id)"
                      />
                    </div>
                  </div>
                </div>
                <div
                  v-if="activeBanner === category.id"
                  class="q-mb-md course-catalog-banner-active"
                >
                  <q-banner class="bg-grey-3 banner" rounded>
                    <p
                      class="courses-purpose text-secondary text-h6 q-mb-sm text-uppercase text-bold"
                    >
                      Цель курса?
                      <q-icon name="mdi-bullseye-arrow" style="margin-bottom: 3px" />
                    </p>
                    <p
                      class="courses-purpose-description text-primary q-mb-md ellipsis-2-lines"
                      style="max-width: 700px"
                    >
                      {{ category.target }}
                    </p>
                    <p
                      class="courses-time text-secondary text-h6 q-mb-sm text-uppercase text-bold"
                    >
                      Время прохождения
                      <q-icon name="schedule" style="margin-bottom: 3px" />
                    </p>
                    <p
                      class="courses-time-item text-primary q-mb-sm text-lowercase"
                      style="max-width: 700px"
                    >
                      {{ category.time }} минут
                    </p>
                  </q-banner>
                </div>
              </li>
            </ul>
          </div>

          <div v-else-if="currentView === 'isFullListView'" class="list">
            <ul class="categories-list">
              <li
                v-for="category in catalogItems"
                :key="category.id"
                class="list-item"
              >
                <div class="row justify-between">
                  <div class="active-hover row">
                    <p
                      class="list-text text-body1 text-primary text-weight-bold q-mr-sm underline-text"
                    >
                      <router-link
                        :to="{ path: category.path+/:+ category.id }"
                        style="text-decoration: none; color: #002882 !important;"
                      >
                        {{ category.title }}
                      </router-link>
                    </p>
                  </div>
                </div>
                <div class="q-mb-md course-catalog-banner-active">
                  <q-banner class="bg-grey-3 banner" rounded>
                    <p
                      class="courses-purpose text-secondary text-h6 q-mb-sm text-uppercase text-bold"
                    >
                      Цель курса?
                      <q-icon name="mdi-bullseye-arrow" style="margin-bottom: 3px" />
                    </p>
                    <p
                      class="courses-purpose-description text-primary q-mb-md ellipsis-2-lines"
                      style="max-width: 700px"
                    >
                      {{ category.target }}
                    </p>
                    <p
                      class="courses-time text-secondary text-h6 q-mb-sm text-uppercase text-bold"
                    >
                      Время прохождения
                      <q-icon name="schedule" style="margin-bottom: 3px" />
                    </p>
                    <p
                      class="courses-time-item text-primary q-mb-sm text-lowercase"
                      style="max-width: 700px"
                    >
                      {{ category.time }} минут
                    </p>
                  </q-banner>
                </div>
              </li>
            </ul>
          </div>

          <div v-else class="list-card">
            <ul class="categories-list categories-card">
              <li
                v-for="category in catalogItems"
                :key="category.id"
                class="card-item"
              >
                <div
                  class="card-container q-py-md row row wrap"
                  style="margin-left: 1px"
                >
                  <router-link
                    :to="{ path: category.path +/:+ category.id }"
                    style="text-decoration: none;"
                  >
                    <q-card
                      class="my-card shadow-3 column"
                      bordered
                    >
                      <q-card-section horizontal>
                        <q-card-section class="q-pt-xs">
                          <div
                            class="category-title text-h5 q-mt-sm q-mb-xs text-weight-bold ellipsis-2-lines"
                            style="font-size: 20px; color: #002882;"
                          >
                            {{ category.title }}
                          </div>
                          <div
                            class="course-goal text-subtitle1 q-mt-sm q-mb-xs"
                            style="font-size: 15px; font-weight: 500;  color: #002882;"
                          >
                            Цель курса:
                            <div
                              class="text-caption text-grey ellipsis-2-lines"
                              style="font-size: 15px;"
                            >
                              {{ category.target }}
                            </div>
                          </div>
                        </q-card-section>
                      </q-card-section>

                      <div class="course-time">
                        <q-icon
                          flat
                          size="large"
                          name="schedule"
                          color="secondary"
                          style="margin-top: 1px"
                          class="time-icon"
                        />
                        <div flat class="q-ml-sm card-time"  style="color: #002882; font-size: 15px;">
                          {{ category.time }} мин
                        </div>
                      </div>

                      <div class="card-buttons q-mt-sm row">
                        <q-btn
                          class="start-course"
                          color="secondary"
                          text-color="white"
                          label="Назначить курс"
                          padding="4px 8px"
                          size="10px"
                          to="#"
                        />
                        <q-btn
                          class="course-details"
                          outline
                          color="secondary"
                          text-color="secondary"
                          label="Подробнее"
                          padding="4px 8px"
                          size="10px"
                          to="#"
                        />
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
export default {
  data() {
    return {
      tab: '', // Активная вкладка
      currentView: 'isListView', // Вид по умолчанию
      activeBanner: null, // ID активного баннера
      catalogItems: [], // Список элементов каталога
    };
  },
  methods: {
    toggleListView(viewType) {
      this.currentView = viewType; // Обновляем текущий вид
      this.saveViewToLocalStorage(); // Сохраняем выбор в localStorage
    },
    toggleBanner(categoryId) {
      this.activeBanner = this.activeBanner === categoryId ? null : categoryId;
    },
    reloadPage(tabId) {
      this.tab = tabId; // Обновляем значение активной вкладки
      const path = `/content-items/${tabId}`;

      // Осуществляем переход на новый маршрут
      this.$router.push(path);
    },
    saveViewToLocalStorage() {
      localStorage.setItem('contentView', this.currentView); // Сохраняем текущий вид в localStorage
    },
    loadViewFromLocalStorage() {
      const savedView = localStorage.getItem('contentView');
      if (savedView) {
        this.currentView = savedView; // Восстанавливаем вид из localStorage
      }
    }
  },
  created() {
    this.loadViewFromLocalStorage(); // Загружаем выбранный вид при создании компонента
  }
};
</script>
