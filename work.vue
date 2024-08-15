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
