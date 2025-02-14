<q-btn
  class="add-task-button"
  label="Добавить задачу"
  color="secondary"
  @click="openModal"
/>

<!-- Диалоговое окно для добавления задачи -->
<q-dialog v-model="isModalOpen" persistent>
  <q-card style="min-width: 400px;">
    <q-card-section>
      <div class="q-gutter-md">
        <q-input
          label="Описание задачи"
          type="text"
          outlined
          required
        />
        <q-input
          label="Срок исполнения"
          type="date"
          outlined
          required
        />
      </div>
    </q-card-section>

    <q-card-actions align="right">
      <q-btn
        flat
        label="Отмена"
        color="grey"
        @click="closeModal"
      />
      <q-btn
        flat
        label="Сохранить"
        color="secondary"
        @click="saveTask"
      />
    </q-card-actions>
  </q-card>
</q-dialog>

return {
  isModalOpen,
  openModal,
  closeModal,
  saveTask,
  monthTasks,
  showToast,
  toastVisible,
  toastMessage,
};

