<script>
import { ref, onMounted, nextTick } from "vue";
import axios from "axios";

const BACKEND_URL =
  window.location.origin + 
const BACKEND_POST_URL =
  window.location.origin + 

export default {
  setup() {


    const changeStepperStyle = () => {
      document.querySelectorAll(".q-stepper__tab").forEach((el) => {
        el.style.paddingLeft = "5px";
        el.style.paddingRight = "5px";
      });
    };

    const changeFontSizeStyle = () => {
      document.querySelectorAll(".q-stepper__title").forEach((el) => {
        el.style.fontSize = "14px";
      });
    };

    const changeAnotherFontSizeStyle = () => {
      document.querySelectorAll(".q-stepper__caption").forEach((el) => {
        el.style.fontSize = "12px";
      });
    };

    onMounted(() => {
      fetchCabinetData();
      fetchMaterialsData();
      fetchCommentListData();
      fetchUsersData();
      fetchCoworkers();

      const observer = new MutationObserver(async (mutationsList) => {
        await nextTick();

        // Выполняем стилизацию при любом изменении DOM
        mutationsList.forEach(() => {
          changeStepperStyle();
          changeFontSizeStyle();
          changeAnotherFontSizeStyle();
        });
      });

      observer.observe(document.body, {
        childList: true,
        subtree: true,
        attributes: true, // Отслеживание изменений атрибутов
      });
    });

    return {
      activeTab,
      toastVisible,
      toastMessage,
      printPage,
      cabinetData,
      usersData,
      materialsData,
      commentListData,
      taskData,
      isCoworkersModalOpen,
      coworkersData,
    };
  },
};
</script>

