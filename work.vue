<li class="table-row-item date-pick">
  <q-input 
    v-model="inputDate" 
    filled 
    label="Выберите дату" 
    readonly 
    @click="showDatePicker = true"
  >
    <template v-slot:append>
      <q-icon name="event" class="cursor-pointer" @click="showDatePicker = true" />
    </template>

    <q-popup-proxy 
      v-model="showDatePicker" 
      transition-show="scale" 
      transition-hide="scale"
    >
      <q-date 
        v-model="selectedDate" 
        mask="YYYY-MM-DD" 
        :model-value="selectedDate" 
        @update:model-value="updateFormattedDate" 
      />
    </q-popup-proxy>
  </q-input>
</li>

const selectedDate = ref('2025-03-23'); // Начальная дата
const showDatePicker = ref(false);
const inputDate = ref('');

// Обновляем значение в инпуте при выборе даты
const updateFormattedDate = (val) => {
  selectedDate.value = val;
  inputDate.value = date.formatDate(val, 'DD.MM.YYYY');
  showDatePicker.value = false;
};

// Следим за изменением selectedDate и обновляем q-date вручную
watch(selectedDate, (newDate) => {
  // Принудительно обновляем q-date, чтобы q-date-header отображал правильную дату
  if (newDate) {
    inputDate.value = date.formatDate(newDate, 'DD.MM.YYYY');
  }
});
