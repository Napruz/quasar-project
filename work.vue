<template>
  <q-card class="my-card q-mt-md">
    <!-- Заголовки табов -->
    <div
      class="small-card-titles row"
      style="justify-content: space-evenly; background-color: #0d2a7d"
    >
      <p
        class="small-card-title materials-title"
        :class="{ 'current-title': activeTab === 'materials' }"
        style="width: 50%; cursor: pointer"
        @click="activeTab = 'materials'"
      >
        Материалы
      </p>
      <p
        class="small-card-title members-title"
        :class="{ 'current-title': activeTab === 'members' }"
        style="width: 50%; cursor: pointer"
        @click="activeTab = 'members'"
      >
        Участники
      </p>
    </div>

    <!-- Контент для вкладки Материалы -->
    <div v-if="activeTab === 'materials'" class="small-card-info materials-card column">
      <ul class="card-info-list">
        <li class="card-info-item">
          <a href="#" download class="attempts-link">Материалы</a>
        </li>
        <li class="card-info-item">
          <a href="#" download class="attempts-link">Программа тренинга</a>
        </li>
        <li class="card-info-item">
          <a href="#" download class="attempts-link">Дополнительно</a>
        </li>
      </ul>
    </div>

    <!-- Контент для вкладки Участники -->
    <div v-if="activeTab === 'members'" class="small-card-info members-card column">
      <ul class="card-info-list">
        <li class="card-info-item">
          <q-img
            class="rounded-borders card-image member-photo"
            style="max-width: 100px; max-height: 100px"
            src="../images/aleksandra.png"
            alt="Фото участника"
          />
          <p class="member-name-info">
            Иванов Иван Иванович <br />
            Директор <br />
            ОП Воркута
          </p>
        </li>
        <li class="card-info-item">
          <q-img
            style="max-width: 100px; max-height: 100px"
            class="rounded-borders card-image member-photo"
            src="../images/default-avatar.png"
            alt="Фото участника"
          />
          <p class="member-name-info">
            Иванов Иван Сергеевич <br />
            Директор <br />
            ОП Тверь
          </p>
        </li>
        <li class="card-info-item">
          <q-img
            style="max-width: 100px; max-height: 100px"
            class="rounded-borders card-image member-photo"
            src="../images/madina.png"
            alt="Фото участника"
          />
          <p class="member-name-info">
            Иванов Иван Сергеевич <br />
            Директор <br />
            ОП Тула
          </p>
        </li>
      </ul>
    </div>
  </q-card>
</template>

<script>
export default {
  data() {
    return {
      activeTab: 'members', // Начальная активная вкладка
    };
  },
};
</script>
.current-title {
  color: #ffffff;
  font-weight: bold;
  border-bottom: 2px solid #ffffff; /* Подчеркивание для активной вкладки */
}

.small-card-title {
  text-align: center;
  padding: 10px 0;
  color: #b0b0b0; /* Цвет неактивных табов */
  transition: color 0.3s ease, border-bottom 0.3s ease;
}

.small-card-title:hover {
  color: #ffffff; /* Цвет при наведении */
}
