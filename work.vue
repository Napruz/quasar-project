<!-- Назначенные встречи -->
<div class="employee-adaptation-container text-info-item column">
  <div class="text-info-wrapper meeting-modal column" style="margin-top: auto;">
    <div class="metting-link-wrapper column">
      <q-btn v-if="employee.status == 'active' || employee.status == 'planned'" 
             color="primary" text-color="white" label="НАЗНАЧИТЬ ВСТРЕЧУ" 
             size="sm" padding="sm" 
             @click="createNewMeeting(employee.person.person_id)"/>
      
      <!-- Форма создания новой встречи -->
      <q-dialog v-if="meetingModalId === employee.person.person_id" 
                v-model="newMeetingModalOpen">
        <q-card class="q-pa-md" 
                style="min-width: 600px; max-width: 800px; box-shadow: 0 4px 12px rgba(0,0,0,0.2);">
          <q-card-section class="q-pb-none">
            <div class="text-h6 q-mb-md text-primary">Назначение встречи по адаптации</div>
          </q-card-section>
          <q-separator spaced/>
          <q-card-section>
            <div class="q-gutter-md column">
              <div class="row items-center q-gutter-sm">
                <div class="text-subtitle2" style="width: 80px; margin-left: 8px;">Кому:</div>
                <div class="row">
                  <span style="margin-right: 10px;">{{ employee.person.person_email }}</span>
                  <span>Тут будет email руководителя</span>
                </div>
              </div>
              
              <div class="row items-center q-gutter-sm">
                <div class="text-subtitle2" style="width: 120px;">Дополнительные участники встречи:</div>
                <!-- ИСПРАВЛЕНО: убрано :value и оставлен только v-model -->
                <q-input dense filled 
                         v-model="newMeetingEmailDisplay" 
                         class="col"/>
                <q-btn dense flat round icon="add" 
                       @click="collaboratorDialogOpen = true"/>
                
                <!-- Диалоговое окно по поиску сотрудника -->
                <q-dialog v-model="collaboratorDialogOpen">
                  <q-card style="min-width: 400px; max-width: 600px;">
                    <q-card-section>
                      <div class="text-h6">Выбор сотрудника</div>
                    </q-card-section>
                    <q-card-section class="q-pt-none">
                      <div class="row q-col-gutter-md q-mb-sm">
                        <div class="col">
                          <q-input v-model="collaboratorSearch" 
                                   placeholder="Поиск сотрудника..." 
                                   outlined dense 
                                   @keyup.enter="fetchCollaboratorSearch"/>
                        </div>
                        <div>
                          <q-btn color="primary" label="Найти" 
                                 @click="fetchCollaboratorSearch"/>
                        </div>
                      </div>
                      <q-list bordered>
                        <q-item v-for="item in collaboratorListData" 
                                :key="item.person_id" 
                                clickable 
                                @click="selectCollaborator(item)">
                          <q-item-section>{{ item.fullname }}</q-item-section>
                        </q-item>
                      </q-list>
                      <div class="row justify-between items-center q-mt-sm">
                        <q-btn flat label="Назад" 
                               :disable="collaboratorPage <= 1" 
                               @click="collaboratorPage-- && fetchCollaboratorList()"/>
                        <q-btn flat label="Вперёд" 
                               @click="collaboratorPage++ && fetchCollaboratorList()"/>
                      </div>
                    </q-card-section>
                    <q-card-actions align="right">
                      <q-btn flat label="Закрыть" v-close-popup/>
                    </q-card-actions>
                  </q-card>
                </q-dialog>
              </div>
              
              <!-- Остальные поля формы -->
            </div>
          </q-card-section>
          <q-card-actions align="right">
            <q-btn flat label="Закрыть" color="primary" 
                   @click="handleDialogClose"/>
            <q-btn label="Отправить" color="primary" flat 
                   @click="postNewMeeting(employee.id)"/>
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </div>
</div>

<script>
import { ref, onMounted, onUnmounted, watch, computed } from "vue";

export default {
  setup() {
    // ... другие переменные ...
    
    const newMeetingEmail = ref([]); // массив для хранения email'ов
    const newMeetingEmailDisplay = ref(''); // строка для отображения в input
    
    // Вычисляемое свойство для синхронизации массива и строки
    const updateEmailDisplay = () => {
      newMeetingEmailDisplay.value = newMeetingEmail.value.join('; ');
    };
    
    // Наблюдатель за массивом email'ов
    watch(newMeetingEmail, () => {
      updateEmailDisplay();
    }, { deep: true });
    
    const selectCollaborator = (item) => {
      if (item.email && !newMeetingEmail.value.includes(item.email)) {
        newMeetingEmail.value.push(item.email);
        // Обновляем отображение
        updateEmailDisplay();
      }
      collaboratorDialogOpen.value = false;
    };
    
    // Также нужно обновлять массив при ручном редактировании input
    const onEmailInputChange = () => {
      // Разбиваем строку по разделителям (точка с запятой или запятая)
      const emails = newMeetingEmailDisplay.value
        .split(/[;,]/)
        .map(email => email.trim())
        .filter(email => email !== '');
      
      // Обновляем массив, удаляя дубликаты
      newMeetingEmail.value = [...new Set(emails)];
    };
    
    // В разметке добавьте @input на q-input:
    // <q-input dense filled v-model="newMeetingEmailDisplay" 
    //          @input="onEmailInputChange" class="col"/>
    
    const resetMeetingForm = () => {
      newMeetingDateStart.value = '';
      newMeetingTimeStart.value = '';
      newMeetingDateEnd.value = '';
      newMeetingTimeEnd.value = '';
      newMeetingEmail.value = [];
      newMeetingEmailDisplay.value = ''; // сбрасываем отображение
      newMeetingTopic.value = 'Встреча по адаптации';
      newMeetingLocation.value = '';
      collaboratorDialogOpen.value = false;
    };
    
    return {
      // ... другие переменные ...
      newMeetingEmail,
      newMeetingEmailDisplay,
      selectCollaborator,
      onEmailInputChange,
      resetMeetingForm
    };
  }
};
</script>
