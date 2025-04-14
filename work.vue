<q-btn
  color="secondary"
  icon="send"
  label="Отправить ИПВД в 1С"
  class="print-page-btn"
  size="sm"
  @click="openSendDialog(cabinetData.id)"
  style="left: 160px;"
/>

<!-- Диалог подтверждения -->
<q-dialog v-model="isSendDialogOpen">
  <q-card style="min-width: 400px">
    <q-card-section class="text-h6">
      Подтверждение отправки
    </q-card-section>

    <q-card-section>
      При нажатии на кнопку <b>«Отправить ИПВД в 1С»</b> ИПВД будет автоматически направлен в 1С на подписание сотруднику.
    </q-card-section>

    <q-card-actions align="right">
      <q-btn flat label="Отмена" color="primary" v-close-popup />
      <q-btn
        label="Отправить ИПВД в 1С"
        color="positive"
        @click="confirmSendTo1C"
      />
    </q-card-actions>
  </q-card>
</q-dialog>

import { ref } from "vue";

export default {
  setup() {
    const isSendDialogOpen = ref(false);
    const currentSendId = ref(null);

    const openSendDialog = (id) => {
      currentSendId.value = id;
      isSendDialogOpen.value = true;
    };

    const confirmSendTo1C = async () => {
      if (currentSendId.value) {
        await printPage1C(currentSendId.value);
        isSendDialogOpen.value = false;
        currentSendId.value = null;
      }
    };

    const printPage1C = async (id) => {
      console.log("Отправка ИПВД в 1С", id);

      // Тут будет реальная отправка — пока заглушка:
      window.location.href = `/download_file.html?file_id=${id}`;

      // Можно заменить на POST-запрос к API, если нужно
    };

    return {
      isSendDialogOpen,
      currentSendId,
      openSendDialog,
      confirmSendTo1C,
    };
  },
};
