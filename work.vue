<template>
  <div class="main-container row justify-between">
    <div class="content-container">
      <!-- Карточка мероприятия -->
      <div class="new-card">
        <q-card class="my-card">
          <!-- ОСНОВНОЕ СОДЕРЖАНИЕ -->
          <q-card-section class="q-pt-none">
            <div class="container row">
              <div class="column items-start q-pl-sm q-ml-xl q-mt-sm">
                <!-- Табы -->
                <div class="event-tabs row">
                  <p
                    :class="['event-tab-item', activeTab === 'info' ? 'event-tab-current' : '']"
                    @click="activeTab = 'info'"
                  >
                    Информация о мероприятии
                  </p>
                  <p
                    :class="['event-tab-item', activeTab === 'participants' ? 'event-tab-current' : '']"
                    @click="activeTab = 'participants'"
                  >
                    Участники
                  </p>
                  <p
                    :class="['event-tab-item', activeTab === 'teachers' ? 'event-tab-current' : '']"
                    @click="activeTab = 'teachers'"
                  >
                    Преподаватели
                  </p>
                </div>

                <!-- Содержимое табов -->
                <!-- Информация о мероприятии -->
                <div v-if="activeTab === 'info'">
                  <!-- Сюда перенеси содержимое информации о мероприятии -->
                  <div class="education-centre q-mt-lg row">
                    <q-icon
                      name="fa-solid fa-building-columns"
                      class="text-primary q-pa-none"
                      size="md"
                    />
                    <p class="education-centre-name text-black" style="margin: auto 15px;">
                      Учебная организация: <span class="text-black">ООО "Аккадемия продаж"</span>
                    </p>
                  </div>

                  <!-- Остальное содержимое для информации о мероприятии -->
                </div>

                <!-- Участники -->
                <div v-if="activeTab === 'participants'">
                  <ul class="categories-list">
                    <li class="list-item">
                      <p class="search-results active-hover text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                        Сергеев Александр Сильвестрович
                      </p>
                    </li>
                    <li class="list-item">
                      <p class="search-results active-hover text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                        Кузьмин Андрей Александрович
                      </p>
                    </li>
                    <li class="list-item">
                      <p class="search-results active-hover text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                        Иванов Иван Иванович
                      </p>
                    </li>
                  </ul>
                </div>

                <!-- Преподаватели -->
                <div v-if="activeTab === 'teachers'">
                  <div class="teachers-container">
                    <ul class="teachers-list">
                      <li class="teacher-item row">
                        <q-img
                          class="rounded-borders card-image"
                          src="../images/aleksandra.png"
                          alt="Фото преподавателя"
                        />
                        <p class="search-results active-hover text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                          Кузовова Александра Сергеевна
                        </p>
                      </li>
                      <li class="teacher-item row">
                        <q-img
                          class="rounded-borders card-image"
                          src="../images/madina.png"
                          alt="Фото преподавателя"
                        />
                        <p class="search-results active-hover text-body1 text-primary text-weight-bold q-mr-sm underline-text">
                          Абдулвагабова Мадина Абдулвагабовна
                        </p>
                      </li>
                    </ul>
                  </div>
                </div>

              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activeTab: 'info', // По умолчанию показываем информацию о мероприятии
    };
  },
};
</script>

<style scoped>
.event-tab-item {
  cursor: pointer;
  padding: 10px;
}
.event-tab-current {
  font-weight: bold;
  text-decoration: underline;
}
</style>

