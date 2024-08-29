<template>
  <q-expansion-item
    v-for="category in categories"
    :key="category.id"
    :icon="category.icon"
    icon-class="text-white"
    label-class="text-white"
    expand-icon-class="text-white"
    expand-icon-toggle
    class="text-white link-text"
    :model-value="openedItem === category.id"
    :hide-expand-icon="!hasChildLinks(category.id)"
    @update:model-value="(value) => onItemToggle(category.id, value)"
  >
    <template v-slot:header>
      <div
        class="row items-center q-gutter-md cursor-pointer hoverable-header"
        style="margin-right: auto;"
        @click.stop="goToFrame(category.path)"
      >
        <q-icon :name="category.icon" class="text-white" />
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
</template>

<script>
import { ref, onMounted, nextTick } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";

export default {
  name: "MyLayout",
  setup() {
    const leftDrawerOpen = ref(false);
    const search = ref("");
    const router = useRouter();
    const openedItem = ref("");
    const userArray = ref([]);
    const menuLinks = ref([]);
    const categories = ref([]);

    const BACKEND_URL = "";

    const fetchMenuLinks = async () => {
      try {
        const collection_code = "";
        const url = BACKEND_URL;
        const params = { collection_code: collection_code };
        const response = await axios.post(
          url,
          new URLSearchParams(params).toString()
        );
        menuLinks.value = response.data.results;
        categories.value = response.data.results.filter(
          (link) => link.parent_id === ""
        );
      } catch (error) {
        console.error("Ошибка загрузки меню", error);
      }
    };

    const fetchUsersInfo = async () => {
      try {
        const url = BACKEND_URL;
        const params = { collection_code: "" };
        const response = await axios.post(
          url,
          new URLSearchParams(params).toString()
        );
        userArray.value = response.data.results[0];
      } catch (error) {
        console.error("Ошибка загрузки данных сотрудника", error);
      }
    };

    onMounted(async () => {
      await fetchMenuLinks();
      await fetchUsersInfo();

      // Ждем обновления DOM с помощью nextTick
      await nextTick();

      // Находим все элементы с role="listitem"
      const listItemElements = document.querySelectorAll('[role="listitem"]');
      console.log(listItemElements);

      listItemElements.forEach((element) => {
        // Добавляем обработчик для hover эффекта
        element.addEventListener('mouseenter', () => {
          element.style.backgroundColor = 'rgba(255, 255, 255, 0.2)';
          console.log("mouse enter");
        });

        // Убираем эффект при уходе курсора
        element.addEventListener('mouseleave', () => {
          element.style.backgroundColor = ''; // Убираем цвет фона
          console.log("mouse leave");
        });
      });
    });

    const getChildLinks = (id) => {
      return menuLinks.value.filter((link) => link.parent_id === id);
    };

    const hasChildLinks = (id) => {
      return getChildLinks(id).length > 0;
    };

    function toggleLeftDrawer() {
      leftDrawerOpen.value = !leftDrawerOpen.value;
    }

    function navigate(path) {
      window.location.href = path;
    }

    function removeNbsp(text) {
      if (text.includes("&nbsp;")) {
        return text.replace(/&nbsp;/g, " ");
      } else {
        return text;
      }
    }

    function onItemToggle(item, value) {
      if (value) {
        openedItem.value = item;
      } else {
        openedItem.value = "";
      }
    }

    return {
      leftDrawerOpen,
      search,
      toggleLeftDrawer,
      navigate,
      openedItem,
      onItemToggle,
      getChildLinks,
      removeNbsp,
      hasChildLinks,
    };
  },
  methods: {
    goToFrame(id) {
      if (id == "https://portal/") {
        window.location.href = id;
      } else {
        const encodedId = encodeURIComponent(id);
        this.$router.push(`/frame/${encodedId}`);
      }
    },
  },
};
</script>
