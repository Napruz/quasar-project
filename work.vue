<template>
  <q-toggle
  v-model="task.completed"
  :false-value="false"
  :true-value="true"
  :label="task.completed ? 'Выполнено' : 'Не выполнено'"
  color="green"
  unchecked-color="red"
  @update:model-value="showToast"
/>
  <q-toast ref="toast" position="top-right" color="green" />
</template>

<script>
  methods: {
  showToast() {
    this.$q.notify({
      message: "Задача сохранена",
      color: "green",
      position: "top-right",
      timeout: 2000
    });
  }
}
</script>  



