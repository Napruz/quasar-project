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

          <!-- Обновленный q-expansion-item -->
          <q-expansion-item
            v-for="category in categories"
            :key="category.id"
            class="text-white link-text"
            :model-value="openedItem === category.id"
            @update:model-value="(value) => onItemToggle(category.id, value)"
          >
            <!-- Кликабельный заголовок -->
            <template v-slot:header>
              <div @click.stop="goToFrame(category.path)">
                <q-icon :name="category.icon" class="text-white q-mr-sm" />
                <span class="text-white">{{ removeNbsp(category.text) }}</span>
              </div>
            </template>

            <!-- Дочерние элементы -->
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

  </q-layout>
</template>

<script>
export default {
  name: "MyLayout",
  methods: {
    goToFrame(id) {
      const encodedId = encodeURIComponent(id);
      this.$router.push(`/frame/${encodedId}`);
    }
  }
};
</script>
