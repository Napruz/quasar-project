<template>
  <div class="progress-bar-container">
    <!-- Блок с текстом -->
    <div class="text-info-container row">
      <!-- Помощник по адаптации -->
      <div class="text-info-item column">
        <div class="text-info-wrapper">
          <p class="text-info-title text-grey q-mb-sm">
            Помощник по адаптации
          </p>
        </div>
        <div class="text-info-wrapper">
          <p class="text-info-value text-secondary">
            {{ mentorFullname }}
          </p>
        </div>
      </div>

      <!-- Назначенные встречи -->
      <div class="text-info-item column">
        <div class="text-info-wrapper">
          <p class="text-info-title text-grey q-mb-sm">
            Назначенные встречи
          </p>
        </div>
        <div class="text-info-wrapper meeting-modal">
          <p class="text-info-value text-secondary">
            {{ userEventsLength }} встреча
          </p>

          <q-btn
            color="grey"
            icon="fa-solid fa-info"
            size="6px"
            push
            round
            class="metting-modal-button"
            style="margin-bottom: auto; margin-left: 10px"
            @click="isMeetingModalOpen = true"
          >
            <q-tooltip
              v-for="(event, index) in usersData.events"
              :key="index"
            >
              {{ event.date_start }}
            </q-tooltip>
          </q-btn>

          <!-- Модальное окно для назначенных встреч -->
          <q-dialog v-model="isMeetingModalOpen">
            <q-card>
              <q-card-section>
                <div class="text-h6" style="padding: 10px; padding-bottom: 0px">
                  Назначенные встречи
                </div>
              </q-card-section>

              <q-card-section>
                <ul style="padding: 10px">
                  <li
                    v-for="(eventData, indexData) in usersData.events"
                    :key="indexData"
                    style="margin-bottom: 15px"
                  >
                    <div class="column">
                      <span>{{ eventData.title }}</span>
                      <span>Начало встречи: {{ eventData.date_start }}</span>
                      <span>Окончание встречи: {{ eventData.date_end }}</span>
                      <span>Участники встречи: {{ eventData.persons }}</span>
                      <span>Встречу назначил: {{ eventData.author }}</span>
                      <span>Комментарий: {{ eventData.comment }}</span>
                    </div>
                  </li>
                </ul>
              </q-card-section>

              <q-card-actions align="right">
                <q-btn flat label="Закрыть" color="primary" v-close-popup />
              </q-card-actions>
            </q-card>
          </q-dialog>
        </div>
      </div>

      <!-- Завершение адаптации -->
      <div class="text-info-item column">
        <div class="text-info-wrapper">
          <p class="text-info-title text-grey q-mb-sm">
            Завершение адаптации
          </p>
        </div>
        <div class="text-info-wrapper">
          <p class="text-info-value text-secondary">
            {{ usersData.finish_date }}
          </p>
        </div>
      </div>
    </div>

    <!--Процесс адаптации в днях-->
    <div class="linear-progress-container column" style="margin-top: 15px">
      <p class="text-bold" style="margin-left: 20px; font-size: 16px">
        Прогресс адаптации (в днях):
        {{ usersData.now_day_count }} / {{ usersData.all_day_count }}
      </p>

      <q-linear-progress
        :value="usersData.all_day_count > 0
          ? usersData.now_day_count / usersData.all_day_count
          : 0"
        size="xl"
        color="secondary"
        style="width: 95%; max-width: 900px; margin-left: 20px"
      />
    </div>

    <!-- Горизонтальный прогресс-бар Stepper -->
    <div class="progress-stepper">
      <q-stepper
        v-model="step"
        ref="stepper"
        color="secondary"
        flat
        alternative-labels
      >
        <q-step
          v-for="(stage, index) in usersData.stages"
          :key="index"
          :name="index"
          :title="stage.name"
          icon="fa-solid fa-check"
          color="secondary"
          class="progress-stepper-item"
          :done="stage.percent == 100"
          :disable="stage.percent != 100"
        >
          {{ stage.description || '' }}
        </q-step>
      </q-stepper>
    </div>

    <!-- Контейнер для круглых диаграмм -->
    <div class="circle-container row">
      <div class="circle-item row">
        <q-circular-progress
          show-value
          reverse
          :value="Math.trunc(userSecondStage)"
          size="60px"
          :thickness="0.22"
          color="light-blue"
          track-color="grey-3"
          class="q-ma-md"
        >
          {{ Math.trunc(userSecondStage) }}%
        </q-circular-progress>
        <p class="text-secondary" style="margin: auto 0">
          Задачи на <br /> испытательный срок
        </p>
      </div>

      <div class="circle-item row">
        <q-circular-progress
          show-value
          reverse
          :value="Math.trunc(userThirdStage)"
          size="60px"
          :thickness="0.22"
          color="light-blue"
          track-color="grey-3"
          class="q-ma-md"
        >
          {{ Math.trunc(userThirdStage) }}%
        </q-circular-progress>
        <p class="text-secondary" style="margin: auto 0">
          Программа обучения
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// Props из родителя
defineProps({
  mentorFullname: String,
  userEventsLength: Number,
  usersData: Object,
  userSecondStage: Number,
  userThirdStage: Number,
});

// Локальный стейт для модалки и степпера
const isMeetingModalOpen = ref(false);
const step = ref(1);
</script>

<style scoped>
.progress-bar-container {
  /* Добавь свои стили или перенеси */
}
</style>


<script setup>
import ProgressInfo from '@/components/ProgressInfo.vue'; // путь подгони под свой проект!

// Твои данные из родителя
const mentorFullname = ref('Иван Иванов');
const usersData = ref({
  events: [],
  finish_date: '2024-12-31',
  now_day_count: 5,
  all_day_count: 30,
  stages: [
    { name: 'Этап 1', percent: 100, description: 'Завершён' },
    { name: 'Этап 2', percent: 50, description: 'В процессе' },
  ],
});
const userEventsLength = ref(usersData.value.events.length);
const userSecondStage = ref(75); // или твои вычисления
const userThirdStage = ref(50);  // или твои вычисления
</script>

<template>
  <div>
    <!-- Твой основной шаблон -->

    <ProgressInfo
      :mentorFullname="mentorFullname"
      :userEventsLength="userEventsLength"
      :usersData="usersData"
      :userSecondStage="userSecondStage"
      :userThirdStage="userThirdStage"
    />

    <!-- Остальная страница -->
  </div>
</template>
