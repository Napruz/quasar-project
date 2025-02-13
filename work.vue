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

    <q-popup-proxy v-model="showDatePicker" transition-show="scale" transition-hide="scale">
      <q-date 
        v-model="selectedDate" 
        mask="YYYY-MM-DD" 
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

// При загрузке страницы обновляем инпут, если дата уже выбрана
if (selectedDate.value) {
  inputDate.value = date.formatDate(selectedDate.value, 'DD.MM.YYYY');
}
