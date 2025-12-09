<!-- 🔥 ИЗМЕНЕНО: заменили v-model="newMeetingEmail" на newMeetingEmailInput -->
<q-input
  dense filled
  v-model="newMeetingEmailInput"
  class="col"
/>

<q-btn dense flat round icon="add" @click="openCollaboratorDialog" />

<script>
import { ref, computed } from "vue";

export default {
  setup() {
    const newMeetingModalOpen = ref(false);

    // ------------------------------------------------------------
    // 🔥 ИЗМЕНЕНО: храним email всегда как МАССИВ
    // ------------------------------------------------------------
    const newMeetingEmail = ref([]);

    // ------------------------------------------------------------
    // 🔥 ИЗМЕНЕНО: computed для связи массива с q-input (который принимает только строку)
    // ------------------------------------------------------------
    const newMeetingEmailInput = computed({
      get() {
        return newMeetingEmail.value.join("; ");
      },
      set(val) {
        newMeetingEmail.value = val
          .split(";")
          .map(e => e.trim())
          .filter(e => e.length > 0);
      }
    });

    const newMeetingDateStart = ref("");
    const newMeetingDateEnd = ref("");
    const newMeetingTimeStart = ref("");
    const newMeetingTimeEnd = ref("");
    const newMeetingTopic = ref("Встреча по адаптации");
    const newMeetingLocation = ref("");

    const collaboratorListData = ref([]);
    const collaboratorDialogOpen = ref(false);
    const collaboratorSearch = ref("");
    const collaboratorPage = ref(1);

    const resetCollaboratorSearch = () => {
      collaboratorSearch.value = "";
      collaboratorPage.value = 1;
      collaboratorListData.value = [];
    };

    const openCollaboratorDialog = () => {
      collaboratorDialogOpen.value = true;
    };

    const closeCollaboratorDialog = async () => {
      collaboratorDialogOpen.value = false;
      resetCollaboratorSearch();
    };

    // ------------------------------------------------------------
    // 🔥 ИЗМЕНЕНО: теперь newMeetingEmail всегда массив -> push всегда работает
    // ------------------------------------------------------------
    const selectCollaborator = async (item) => {
      if (item.email && !newMeetingEmail.value.includes(item.email)) {
        newMeetingEmail.value.push(item.email); // ← теперь всегда массив
      }
      await closeCollaboratorDialog();
    };

    const resetMeetingForm = () => {
      newMeetingDateStart.value = "";
      newMeetingTimeStart.value = "";
      newMeetingDateEnd.value = "";
      newMeetingTimeEnd.value = "";
      newMeetingEmail.value = [];        // ← всегда массив
      newMeetingTopic.value = "Встреча по адаптации";
      newMeetingLocation.value = "";
      closeCollaboratorDialog();
    };

    const postNewMeeting = async (adaptationId) => {
      try {
        // ------------------------------------------------------------
        // 🔥 ИЗМЕНЕНО: newMeetingEmail точно массив, преобразование больше не нужно
        // ------------------------------------------------------------
        const emailsArray = newMeetingEmail.value;

        const requestBody = {
          wvars: [
            { name: "event_recipient_email", value: emailsArray.join(";") },
            // ... остальное без изменений
          ]
        };

        // отправка запроса ...
      } catch (e) {
        console.error(e);
      }

      newMeetingModalOpen.value = false;
      resetMeetingForm();
    };

    return {
      newMeetingModalOpen,
      newMeetingEmailInput,   // 🔥 ИЗМЕНЕНО → возвращаем новое computed
      openCollaboratorDialog,
      collaboratorDialogOpen,
      collaboratorSearch,
      collaboratorListData,
      collaboratorPage,
      selectCollaborator,
      closeCollaboratorDialog,
      resetMeetingForm,
      postNewMeeting,
      newMeetingDateStart,
      newMeetingDateEnd,
      newMeetingTimeStart,
      newMeetingTimeEnd,
      newMeetingTopic,
      newMeetingLocation
    };
  }
};
</script>
