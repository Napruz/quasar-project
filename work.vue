<template>
  <div class="event-page">
    <!-- Заголовок мероприятия -->
    <p
      class="header-content-title text-3 text-weight-regular text-uppercase text-white text-weight-bold"
    >
      {{ eventData.eventName }}
    </p>

    <!-- Даты мероприятия -->
    <div class="event-dates row" style="margin-bottom: 10px">
      <p
        class="header-content-description event-date-start"
        style="margin-right: 5px"
      >
        {{ eventData.eventDates.split(' - ')[0] }}
      </p>
      <p class="header-content-description">-</p>
      <p
        class="header-content-description event-date-finish"
        style="margin-left: 5px"
      >
        {{ eventData.eventDates.split(' - ')[1] }}
      </p>
    </div>

    <!-- Свободные места -->
    <div class="members-count row">
      <p class="members-item column">
        Свободных мест
        <span style="margin: 0 auto"> {{ eventData.avialableMembers }} </span>
      </p>
    </div>

    <!-- Формат и место проведения -->
    <div class="event-details row">
      <div class="date-finish column q-mr-xl">
        <div class="date-finish-content">
          <p class="main-date-finish-title text-black q-mb-sm">
            Формат проведения
          </p>
        </div>
        <div class="date-finish-item">
          <p class="date-finish-title text-grey">
            {{ eventData.externalStatus ? "Внешнее обучение" : "Очное мероприятие" }}
          </p>
        </div>
      </div>

      <div class="date-finish column q-mr-xl">
        <div class="date-finish-content">
          <p class="main-date-finish-title text-black q-mb-sm">
            Место проведения
          </p>
        </div>
        <div class="date-finish-item">
          <p class="date-finish-title text-grey">{{ eventData.locationEvent }}</p>
        </div>
      </div>
    </div>

    <!-- Ответственные лица -->
    <div class="event-manager row">
      <div class="event-manager-container">
        <p class="education-centre-name text-black q-mb-sm">
          Ответственные за проведение:
        </p>
      </div>
      <div class="event-manager-name">
        <a v-for="member in eventData.members" :key="member.id" :href="'mailto:' + member.email">
          {{ member.fullname }}
        </a>
      </div>
    </div>

    <!-- Участники -->
    <div v-if="activeTab === 'members'" class="small-card-info members-card column">
      <ul class="card-info-list">
        <li v-for="member in eventData.members" :key="member.id" class="card-info-item">
          <q-img
            class="rounded-borders card-image member-photo"
            style="max-width: 100px; max-height: 100px"
            :src="member.photo || '../images/person-default.png'"
            alt="Фото участника"
          />
          <p class="member-name-info">
            {{ member.fullname }} <br />
            {{ member.position || 'Должность не указана' }} <br />
            {{ member.department || 'Отдел не указан' }}
          </p>
        </li>
      </ul>
    </div>

    <!-- Основные темы и цели -->
    <p class="main-purpose-description text-black q-mb-md ellipsis-2-lines">
      {{ eventData.competency || "Информация отсутствует" }}
    </p>

    <ul class="courses-themes-list">
      <li v-for="theme in eventData.materials" :key="theme.id" class="list-item">
        <div class="themes-arrow-items row">
          <div class="q-ml-xs">
            <p class="main-themes-description text-black q-mb-xs">
              {{ theme }}
            </p>
          </div>
        </div>
      </li>
    </ul>

    <!-- Стоимость обучения и обучающая организация -->
    <div v-if="eventData.educationCompany" class="competency column q-mr-xl">
      <div class="competency-content">
        <p class="main-purpose-title text-black q-mb-sm">
          Обучающая организация
        </p>
      </div>
      <div class="competency">
        <p class="competency-title text-grey">
          {{ eventData.educationCompany }}
        </p>
      </div>
    </div>

    <div v-if="eventData.educationPrice" class="competency column q-mr-xl">
      <div class="competency-content">
        <p class="main-purpose-title text-black q-mb-sm">
          Стоимость обучения
        </p>
      </div>
      <div class="competency">
        <p class="competency-title text-grey">
          {{ eventData.educationPrice }}
        </p>
      </div>
    </div>

    <!-- Кнопки управления -->
    <div class="course-buttons row justify-between">
      <q-btn
        v-for="button in eventData.buttons"
        :key="button.label"
        :label="button.label"
        :icon="button.icon"
        class="course-button-item text-white"
        size="15px"
        glossy
        @click="button.action"
      />
    </div>
  </div>
</template>
