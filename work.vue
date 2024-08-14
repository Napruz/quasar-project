<template>
  <q-layout view="lHh Lpr lFf" class="bg-grey-1">
    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      class="bg-grey-2 left-drawer"
      :width="360"
    >
      <q-scroll-area class="fit blue-10 left-drawer">
        <q-list padding>
          <div class="drawer-header row">
            <div class="drawer-header-container row justify-between q-mb-md">
              <q-btn
                v-if="$q.screen.gt.xs"
                flat
                no-caps
                no-wrap
                class="button-logo q-pa-xs"
                :to="{ path: '/' }"
              >
                <q-img
                  class="vtbl-logo rounded-borders"
                  src="icons/vtbl-logo.png"
                  alt="Логотип компании"
                />
              </q-btn>

              <q-btn
                flat
                dense
                round
                class="menu-button text-white q-mt-sm q-ml-md"
                aria-label="Menu"
                icon="fa-solid fa-angles-left"
                @click="toggleLeftDrawer"
              />
            </div>
          </div>

          <q-expansion-item
            v-for="category in categories"
            :key="category.id"
            icon-class="text-white"
            label-class="text-white"
            expand-icon-class="text-white"
            expand-icon-toggle
            class="text-white link-text"
            :model-value="openedItem === category.id"
            :hide-expand-icon="!hasChildLinks(category.id)" <!-- Условие для скрытия иконки -->
            @update:model-value="(value) => onItemToggle(category.id, value)"
          >
            <template v-slot:header>
              <div
                class="row items-center q-gutter-md cursor-pointer"
                @click.stop="goToFrame(category.path)"
              >
                <q-icon :name="category.icon" class="text-white" />
                <span class="text-white">{{ removeNbsp(category.text) }}</span>
              </div>
            </template>

            <q-item
              v-for="link in getChildLinks(category.id)"
              :key="link.id"
              clickable
              v-ripple
              @click="goToFrame(link.path)"
              class="q-pl-lg"
            >
              <q-item-section avatar>
                <q-icon :name="link.icon" class="text-white q-pa-none" />
              </q-item-section>
              <q-item-section class="text-white link-text">
                {{ link.text }}
              </q-item-section>
            </q-item>
          </q-expansion-item>

          <q-separator class="q-my-md" />
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>
<script>
import { ref, computed } from "vue";
import { useRouter } from "vue-router";

export default {
  name: "MyLayout",
  setup() {
    const leftDrawerOpen = ref(true);
    const router = useRouter();
    const categories = ref([]); // Ваши категории
    const openedItem = ref(null);

    // Функция для получения дочерних ссылок
    const getChildLinks = (id) => {
      // Реализуйте логику получения дочерних ссылок по id
      return categories.value.filter(link => link.parent_id === id);
    };

    // Функция проверки наличия дочерних ссылок
    const hasChildLinks = (id) => {
      return getChildLinks(id).length > 0;
    };

    const goToFrame = (id) => {
      if (id === "https://portal/") {
        window.location.href = id;
      } else {
        const encodedId = encodeURIComponent(id);
        router.push(`/frame/${encodedId}`);
      }
    };

    const toggleLeftDrawer = () => {
      leftDrawerOpen.value = !leftDrawerOpen.value;
    };

    const removeNbsp = (text) => {
      return text.replace(/&nbsp;/g, " ");
    };

    return {
      leftDrawerOpen,
      categories,
      openedItem,
      getChildLinks,
      hasChildLinks,
      goToFrame,
      toggleLeftDrawer,
      removeNbsp
    };
  }
};
</script>

