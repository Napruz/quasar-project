methods: {
  toggleBanner(index) {
    this.expandedIndex = this.expandedIndex === index ? null : index;
  },
  transformButton(index) {
    return this.expandedIndex === index ? "transform-button" : "down-button";
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

      this.catalogItems = response.data.results.filter((item) =>
        item.parentCategory.includes(categoryId)
      );

      if (this.catalogItems.length > 0) {
        this.currentCategory = this.catalogItems[0].category; // Исправьте на правильный путь
      }
    } catch (error) {
      console.error("Ошибка при получении данных:", error);
    }
  },
},
