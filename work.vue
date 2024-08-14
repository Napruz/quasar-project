import axios from "axios";
import { ref } from "vue";

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
      currentCategoryTitle: ref(""),
    };
  },
  data() {
    return {
      category: {},
    };
  },
  methods: {
    async fetchCourseData() {
      try {
        const params = {
          collection_code: "vtbl_quasar_course_data",
          parameters: "course_id=" + this.$route.params.id.substring(1),
        };

        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        // Получаем данные курса
        const courseData = response.data.results[0];
        this.category = courseData;

        // Ищем категорию по parentCategory
        const categoryId = courseData.parentCategory[0];
        const categoryName = CATEGORIES.find((cat) => cat.id === categoryId);

        if (categoryName) {
          this.currentCategoryTitle = categoryName.title;
        } else {
          this.currentCategoryTitle = "Категория не найдена";
        }
      } catch (error) {
        console.error("Ошибка загрузки курса", error);
      }
    },
  },
  mounted() {
    this.fetchCourseData();
  },
};

