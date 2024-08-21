<script>
import { ref, onMounted } from "vue";
import axios from "axios";

const BACKEND_URL = "https://als-wt/pp/Ext5/extjs_json_collection_data.html";

// Предопределенные категории
const CATEGORIES = [
  { id: "7207783892693892797", title: "Профессиональные навыки" },
  { id: "7207784069377055908", title: "Личная эффективность и развитие" },
  { id: "7207784147986504834", title: "Вводные курсы (инструктажи)" },
  { id: "7313878051766677160", title: "Управление эффективностью деятельности (УЭД)" },
];

export default {
  setup() {
    return {
      tab: ref(""),
      currentCategoryTitle: ref(""),
    };
  },
  data() {
    return {
      currentView: 'isListView', // По умолчанию isListView
      catalogItems: [],
      activeBanner: null,
    };
  },
  methods: {
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
    // Запрос на сервер
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

        // Инициализируем массив для хранения отфильтрованных курсов
        let filteredCourses = [];

        // Перебираем все курсы
        response.data.results.forEach((item) => {
          // Проверяем, содержит ли массив parentCategory текущий categoryId
          if (item.parentCategory.includes(categoryId)) {
            filteredCourses.push(item);
          }
        });

        // Удаляем дублирующиеся курсы, если они существуют
        this.catalogItems = filteredCourses.filter((item, index, self) =>
          index === self.findIndex((t) => (
            t.id === item.id
          ))
        );

        // Находим название категории по id
        const category = CATEGORIES.find((cat) => cat.id === categoryId);

        if (category) {
          this.currentCategoryTitle = category.title;
          this.tab = categoryId; // Устанавливаем активную вкладку на основе категории
        } else {
          this.currentCategoryTitle = "Категория не найдена";
        }

      } catch (error) {
        console.error("Ошибка при получении данных:", error);
      }
    },
    // Сохраняем текущий вид в localStorage
    saveViewToLocalStorage() {
      localStorage.setItem('selectedView', this.currentView);
    },
  },
  mounted() {
    // Получаем сохраненный вид из localStorage или используем isListView по умолчанию
    const savedView = localStorage.getItem('selectedView');
    if (savedView) {
      this.currentView = savedView;
    }

    this.fetchCatalogItemsData();
  },
  watch: {
    // Следим за изменением currentView и сохраняем новое значение
    currentView(newView) {
      this.saveViewToLocalStorage();
    }
  }
};
</script>
