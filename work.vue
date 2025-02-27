<script setup>
import { nextTick } from 'vue'

const printPage = async () => {
  await nextTick() // Дожидаемся завершения обновлений DOM

  setTimeout(() => {
    window.print()
  }, 300) // Можно увеличить задержку, если нужно
}
</script>

<template>
  <div class="print-page-container">
    <q-btn
      color="grey"
      icon="print"
      label="Печать"
      class="print-page-btn"
      size="sm"
      @click="printPage"
    />
  </div>
</template>

<style>
.print-page-btn {
  position: absolute;
}
</style>
