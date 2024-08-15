export default {
  setup() {
    return {
      tab: ref(""),
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
    changeTab(tab) {
      this.isListView = true;
      switch (tab) {
        case "briefings":
          this.isListView = true;
          this.fetchCatalogItemsData("7207784147986504834");
          break;
        case "efficiency":
          this.isListView = true;
          this.fetchCatalogItemsData("7207784069377055908");
          break;
        case "ued":
          this.isListView = true;
          this.fetchCatalogItemsData("7313878051766677160");
          break;
        default:
          this.fetchCatalogItemsData("7207783892693892797");
      }
    },
    // Новый метод для обработки кликов по вкладкам
    handleTabClick(path) {
      this.$router.push(path);
    },
    async fetchCatalogItemsData() {
      const params = {
        collection_code: "vtbl_quasar_courses_list",
      };
      const categoryId = this.$route.params.id.substring(1);

      try {
        const response = await axios.post(BACKEND_URL, new URLSearchParams(params).toString());
        this.catalogItems = response.data.results.filter((item) => {
          return item.parentCategory.includes(categoryId);
        });

        const category = CATEGORIES.find((cat) => cat.id === categoryId);
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
