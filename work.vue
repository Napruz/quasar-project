<script>
import { ref } from "vue";
import axios from "axios";

const BACKEND_URL = "";

// Предопределенные категории
const CATEGORIES = [
  

export default {
  setup() {
    return {
      tab: ref(""),
      currentCategoryTitle: ref(""),
    };
  },
  data() {
    return {
      currentView: 'listCardView', // По умолчанию listCardView
      catalogItems: [],
      activeBanner: null,
    };
  },
  methods: {
    toggleListView(viewType) {
      this.currentView = viewType; // Обновляем текущий вид
      this.saveViewToSessionStorage(); // Сохраняем выбор в sessionStorage
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

    saveViewToSessionStorage() {
      sessionStorage.setItem('contentView', this.currentView); // Сохраняем текущий вид в sessionStorage
    },

    loadViewFromSessionStorage() {
      const savedView = sessionStorage.getItem('contentView');
      if (savedView) {
        this.currentView = savedView; // Восстанавливаем вид из sessionStorage
      }
    }
  },

  created() {
    this.loadViewFromSessionStorage(); // Загружаем выбранный вид при создании компонента
  },

  mounted() {
    this.fetchCatalogItemsData();
  },
};
</script>

