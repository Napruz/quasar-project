<script>
import { ref } from "vue";
import axios from "axios";

const BACKEND_URL = "";

const CATEGORIES = [
  { id: "7207783892693892797", title: "Профессиональные навыки" },
  { id: "7207784069377055908", title: "Личная эффективность и развитие" },
  { id: "7207784147986504834", title: "Вводные курсы (инструктажи)" },
  { id: "7313878051766677160", title: "Управление эффективностью деятельности (УЭД)" },
];

export default {
  setup() {
    const tab = ref("");
    const currentCategoryTitle = ref("");

    return {
      tab,
      currentCategoryTitle,
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
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        this.catalogItems = response.data.results.filter((item) => {
          return item.parentCategory.includes(categoryId);
        });

        const category = CATEGORIES.find((cat) => cat.id === categoryId);

        if (category) {
          this.currentCategoryTitle = category.title;
          this.tab = category.id; // Устанавливаем активную вкладку
        } else {
          this.currentCategoryTitle = "Категория не найдена";
          this.tab = CATEGORIES[0].id; // Устанавливаем первую категорию, если не найдено
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

