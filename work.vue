const collaboratorDialogOpen = ref(false);
const collaboratorSearch = ref('');
const collaboratorPage = ref(1);
const collaboratorListData = ref([]);

// --- ДОБАВЛЯЕМ ЭТУ ФУНКЦИЮ ---
const resetCollaboratorSearch = () => {
  collaboratorSearch.value = '';
  collaboratorPage.value = 1;
  collaboratorListData.value = [];
};

// Открытие диалога
const openCollaboratorDialog = async () => {
  collaboratorDialogOpen.value = true;
};

// Закрытие диалога
const closeCollaboratorDialog = async () => {
  collaboratorDialogOpen.value = false;
  resetCollaboratorSearch();     // <<< ВАЖНО! СБРОС
};

// Выбор сотрудника
const selectCollaborator = async (item) => {
  if (item.email && !newMeetingEmail.value.includes(item.email)) {
    newMeetingEmail.value.push(item.email);
  }
  closeCollaboratorDialog();     // <<< тут сброс тоже произойдёт
};

// Сброс формы встречи
const resetMeetingForm = () => {
  newMeetingDateStart.value = '';
  newMeetingTimeStart.value = '';
  newMeetingDateEnd.value = '';
  newMeetingTimeEnd.value = '';
  newMeetingEmail.value = [];
  newMeetingTopic.value = 'Встреча по адаптации';
  newMeetingLocation.value = '';

  closeCollaboratorDialog();     // <<< закрытие + сброс поиска
};
