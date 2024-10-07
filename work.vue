<script>
import { ref, onMounted, nextTick, computed } from "vue";
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
    const menuLinks = ref([]); // весь ответ от сервера
    const categories = ref([]); // Заголовки вкладок
    const userURL = computed(() => `${window.location.origin}`);

    const BACKEND_URL =
     

    // Обработчик на иконку в поле поиска
    const onSearchButtonClick = () => {
      // Перенаправление на страницу с параметром word
      router.push({ path: "/search", query: { word: search.value } });
    };

    // Обработчик Enter в поле поиска
    const onSearchEnter = (event) => {
      if (event.key === "Enter") {
        // Перенаправление на страницу с параметром word
        router.push({ path: "/search", query: { word: search.value } });
      }
    };

    const fetchMenuLinks = async () => {
      try {
        // параметры
        const collection_code = "vtbl_quasar_main_menu";
        const url = BACKEND_URL;
        const params = {
          collection_code: collection_code,
        };
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
        const params = {
          collection_code: "vtbl_quasar_user_data",
        };
        // POST запрос
        const response = await axios.post(
          url,
          new URLSearchParams(params).toString()
        );
        userArray.value = response.data.results[0];
      } catch (error) {
        console.error("Ошибка загрузки данных сотрудника", error);
      }
    };

    const mutObserver = async () => {
      let observer = new MutationObserver(function (mutations) {
        console.log(mutations);
        mutations.forEach(function (mutationRecord) {
          document.getElementById("wt-body").style.paddingLeft =
            mutationRecord.target.style.paddingLeft;
        });
      });
      observer.observe(
        document.getElementsByClassName("q-page-container")[0],
        { attributes: true, attributeFilter: ["style"] }
      );
    };

    onMounted(async () => {
      await fetchMenuLinks();
      await fetchUsersInfo();
      await mutObserver();

      // Ждем обновления DOM с помощью nextTick
      await nextTick();

      // Находим все элементы с role="listitem"
      const listItemElements = document.querySelectorAll('[role="listitem"]');
      const searchQfieldNative = document.querySelectorAll(".q-field__native");

      searchQfieldNative.forEach((el) => {
        el.style.paddingLeft = "10px";
      });

      listItemElements.forEach((element) => {
        // Добавляем обработчик для эффекта при наведении курсора
        element.addEventListener("mouseenter", () => {
          element.style.backgroundColor = "rgba(255, 255, 255, 0.2)";
        });

        // Убираем эффект при уходе курсора
        element.addEventListener("mouseleave", () => {
          element.style.backgroundColor = ""; // Убираем цвет фона
        });
      });
    });

    const getChildLinks = (id) => {
      return menuLinks.value.filter((link) => link.parent_id === id);
    };

    // Функция проверки наличия дочерних ссылок
    const hasChildLinks = (id) => {
      return getChildLinks(id).length > 0;
    };

    function toggleLeftDrawer() {
      leftDrawerOpen.value = !leftDrawerOpen.value;
    }

    function normalizeText(text) {
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
      openedItem,
      onItemToggle,
      onSearchEnter,
      onSearchButtonClick,
      categories,
      userArray,
      getChildLinks,
      normalizeText,
      hasChildLinks,
      userURL,
    };
  },

  methods: {
    goToFrame(path) {
      window.location.href = path;
    },
  },
};
</script>
