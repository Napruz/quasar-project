<q-dialog
  v-if="newCommentModalId === task.id"
  v-model="isCommentModalOpen"
>
  <q-card style="min-width: 600px;">
    <q-card-section class="text-h6">
      Добавьте свой комментарий
    </q-card-section>

    <q-card-section>
      <q-input
        v-model="newCommentText"
        type="textarea"
        autogrow
        placeholder="Введите комментарий..."
        outlined
        dense
        :maxlength="1000"
        counter
      />
    </q-card-section>

    <q-card-actions align="right">
      <q-btn
        label="ОК"
        color="primary"
        @click="submitComment(task.id)"
        :disable="!newCommentText.trim()"
      />
      <q-btn
        flat
        label="Закрыть"
        color="dark"
        @click="isCommentModalOpen = false"
      />
    </q-card-actions>
  </q-card>
</q-dialog>

const isCommentModalOpen = ref(false);
const newCommentText = ref("");
const newCommentModalId = ref(null); // для отслеживания, к какому task относится

const submitComment = (taskId) => {
  if (newCommentText.value.trim()) {
    // Тут можно вызвать API или передать комментарий в store
    console.log("Комментарий к задаче", taskId, ":", newCommentText.value);

    // Очистка и закрытие
    newCommentText.value = "";
    isCommentModalOpen.value = false;
    newCommentModalId.value = null;
  }
};
