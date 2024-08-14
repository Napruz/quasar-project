<template>
  <div class="main-container row justify-between">
    <div class="content-container">
    </div>

    <div class="container row">
      <div class="column items-start q-pl-sm q-ml-xl q-mt-sm">
        <div class="header-container">
          <p class="text-3 text-weight-regular text-uppercase text-primary text-weight-bold" style="font-size: 25px">
            {{ currentCategory }}
          </p>
        </div>

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

    <div v-if="isListView" class="list">
      <ul class="categories-list">
        <li v-for="(category, index) in catalogItems" :key="category.id" class="list-item">
          <div class="row justify-between">
            <div class="active-hover row">
              <p class="text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                <router-link :to="category.path" style="text-decoration: none">
                  {{ category.title }}
                </router-link>
              </p>
              <div class="down-button" :class="transformButton(index)">
                <q-btn
                  flat
                  round
                  color="primary"
                  padding="none"
                  size="md"
                  icon="mdi-chevron-down"
                  @click="toggleBanner(index)"
                />
              </div>
            </div>

            <div class="description-button">
              <q-btn
                glossy
                size="10px"
                class="q-ml-xl text-white gradient-button"
                label="Описание"
                :to="{ path: category.path + '/' + category.id }"
              />
            </div>
          </div>

          <div
            v-if="expandedIndex === index"
            class="q-mb-md course-catalog-banner-active"
          >
            <q-banner class="bg-grey-4 banner" rounded>
              <p class="courses-purpose text-secondary text-h6 q-mb-sm text-uppercase text-bold">
                Цель курса?
                <q-icon name="mdi-bullseye-arrow" style="margin-bottom: 3px" />
              </p>
              <p
                class="courses-purpose-description text-primary q-mb-md ellipsis-2-lines"
                style="max-width: 700px"
              >
                {{ category.target }}
              </p>
              <p class="courses-time text-secondary text-h6 q-mb-sm text-uppercase text-bold">
                Время прохождения
                <q-icon name="schedule" style="margin-bottom: 3px" />
              </p>
              <p
                class="courses-time-item text-primary q-mb-sm text-lowercase"
                style="max-width: 700px"
              >
                {{ category.bannerTime }} минут
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
                    <div
                      class="text-h5 q-mt-sm q-mb-xs text-weight-bold ellipsis-1-lines"
                      style="font-size: medium"
                    >
                      {{ category.title }}
                    </div>
                    <div
                      class="text-subtitle1 q-mt-sm q-mb-xs"
                      style="font-size: small; font-weight: 500"
                    >
                      Цель курса?
                    </div>
                    <div class="text-caption text-grey ellipsis-2-lines">
                      {{ category.target }}
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
</template>
