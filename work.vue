<q-toggle
  v-model="task.completed"
  :false-value="false"
  :true-value="true"
  :label="task.completed ? 'Выполнено' : 'Не выполнено'"
  color="green"
  unchecked-color="red"
  @update:model-value="() => showToast('Задача сохранена')"
/>

const showToast = (message = "Задача сохранена") => {
  console.log("Задача сохранена");
  toastMessage.value = message;
  toastVisible.value = true;
  console.log("toastVisible:", toastVisible.value); // Проверка

  setTimeout(() => {
    toastVisible.value = false;
  }, 2000);
};

<div v-show="toastVisible" class="custom-toast">
  {{ toastMessage }}
</div>
