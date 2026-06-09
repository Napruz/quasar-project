<template>
  <q-layout view="lHh Lpr lFf" class="bg-grey-1" style="min-height: 1px !important">

    <!-- Шапка -->
    <q-header elevated class="bg-white text-grey-8" style="width: auto; min-height: 82px; display: flex">
      <q-toolbar class="header-layout">
        <!-- ... шапка без изменений ... -->
      </q-toolbar>
    </q-header>

    <!-- Боковая панель -->
    <q-drawer v-model="leftDrawerOpen" show-if-above bordered class="bg-grey-2 left-drawer" :width="360">
      <q-scroll-area class="fit blue-10 left-drawer">
        <q-list>

          <!-- ... меню без изменений ... -->

          <q-separator class="q-my-md" />

          <div class="calendar-widget q-px-md q-pb-md">
            <div class="event-calendar">

              <!-- СОБЫТИЯ -->
              <div class="events-block">
                <div class="events-title">События</div>
                <div v-if="selectedEvents.length === 0" class="no-events">
                  На выбранную дату событий нет
                </div>
                <q-list v-else dense>
                  <q-item
                    v-for="event in selectedEvents"
                    :key="event.id"
                    clickable v-ripple
                    class="event-item"
                    @click="goToEvent(event.link)"
                  >
                    <q-item-section>
                      <q-item-label class="text-weight-medium">{{ event.title }}</q-item-label>
                      <q-item-label caption class="text-grey-3">{{ event.time }}</q-item-label>
                    </q-item-section>
                  </q-item>
                </q-list>
              </div>

              <!-- КАЛЕНДАРЬ -->
              <div class="calendar-wrapper">
                <q-date
                  ref="calendarRef"
                  v-model="selectedDate"
                  flat bordered minimal
                  mask="YYYY-MM-DD"
                  color="primary"
                  class="custom-calendar"
                  :events="eventDates"
                  :event-color="getEventColor"
                  @update:model-value="onDateClick"
                />
              </div>

            </div>
          </div>

          <q-separator class="q-my-md" />

          <!-- ... футер без изменений ... -->

        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>

    <!-- ✅ Телепорт: тултип рендерится прямо в <body>,
         вне любых overflow:hidden родителей.
         Пишется ВНУТРИ <template>, но СНАРУЖИ всех блоков лэйаута. -->
    <teleport to="body">
      <div
        v-if="tooltip.visible"
        class="calendar-tooltip"
        :style="{
          left: tooltip.x + 'px',
          top: tooltip.y + 'px',
        }"
      >
        <div
          v-for="event in tooltip.events"
          :key="event.id"
          class="tooltip-event"
        >
          <div class="text-weight-bold">{{ event.title }}</div>
          <div class="text-grey-3">{{ event.time }}</div>
        </div>
      </div>
    </teleport>

  </q-layout>
</template>

<script>
import { ref, onMounted, onUnmounted, nextTick, computed } from "vue";
import axios from "axios";

export default {
  name: "MyLayout",
  setup() {
    const leftDrawerOpen = ref(false);
    const searchCatalog = ref("");
    const openedItem = ref("");
    const userArray = ref([]);
    const menuLinks = ref([]);
    const categories = ref([]);
    const userURL = computed(() => `${window.location.origin}`);
    const originURL = computed(() => `${window.location.origin}`);

    const selectedDate = ref(new Date().toISOString().split("T")[0]);
    const datePicker = ref([]);
    const calendarRef = ref(null);

    const monthMap = {
      Январь: "01", Февраль: "02", Март: "03", Апрель: "04",
      Май: "05", Июнь: "06", Июль: "07", Август: "08",
      Сентябрь: "09", Октябрь: "10", Ноябрь: "11", Декабрь: "12",
    };

    const tooltip = ref({ visible: false, x: 0, y: 0, events: [] });

    const calendarEvents = computed(() =>
      datePicker.value.map((event) => ({
        id: event.id,
        title: event.name,
        date: event.start_date ? event.start_date.split("T")[0] : "",
        time:
          event.start_date && event.finish_date
            ? `${event.start_date.slice(11, 16)} - ${event.finish_date.slice(11, 16)}`
            : "",
        link: event.link,
      }))
    );

    const eventsMap = computed(() => {
      const map = {};
      calendarEvents.value.forEach((event) => {
        if (!map[event.date]) map[event.date] = [];
        map[event.date].push(event);
      });
      return map;
    });

    const eventDates = computed(() =>
      Object.keys(eventsMap.value).map((date) => date.replaceAll("-", "/"))
    );

    const selectedEvents = computed(() => eventsMap.value[selectedDate.value] || []);

    const getEventsByDate = (date) => eventsMap.value[date] || [];

    const onDateClick = (date) => { selectedDate.value = date; };

    const goToEvent = (link) => { if (link) window.location.href = link; };

    const getEventColor = (date) => {
      const normalized = date.replaceAll("/", "-");
      return normalized === selectedDate.value ? "white" : "amber";
    };

    const getDisplayedMonthYear = () => {
      const root = calendarRef.value?.$el || calendarRef.value;
      if (!root) return null;
      const blocks = root.querySelectorAll(".q-date__navigation .block");
      if (blocks.length < 2) return null;
      return {
        month: monthMap[blocks[0].textContent.trim()],
        year: blocks[1].textContent.trim(),
      };
    };

    // ✅ Координаты теперь fixed (от viewport), а не от wrapper
    const onCalendarMouseMove = (e) => {
      const root = calendarRef.value?.$el;
      if (!root) return;

      const btn = e.target.closest(".q-date__calendar-days button");
      if (!btn) { tooltip.value.visible = false; return; }

      const dot = btn.querySelector(".q-date__event");
      if (!dot) { tooltip.value.visible = false; return; }

      const day = btn.querySelector(".block")?.textContent?.trim();
      if (!day) return;

      const displayed = getDisplayedMonthYear();
      if (!displayed) return;

      const date = `${displayed.year}-${displayed.month}-${day.padStart(2, "0")}`;
      const events = getEventsByDate(date);
      if (!events.length) { tooltip.value.visible = false; return; }

      const rect = btn.getBoundingClientRect();
      const tooltipWidth = 240;

      // Центрируем по кнопке, но не выходим за края экрана
      let x = rect.left + rect.width / 2 - tooltipWidth / 2;
      x = Math.max(8, Math.min(x, window.innerWidth - tooltipWidth - 8));

      // Над кнопкой — transform translateY(-100%) сдвинет вверх
      const y = rect.top - 8;

      tooltip.value = { visible: true, x, y, events };
    };

    const BACKEND_URL = window.location.origin + `/pp/Ext5/extjs_json_collection_data.html`;

    const fetchMenuLinks = async () => {
      try {
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams({ collection_code: "vtbl_quasar_main_menu" }).toString()
        );
        menuLinks.value = response.data.results;
        categories.value = response.data.results.filter((link) => link.parent_id === "");
      } catch (error) { console.error("Ошибка загрузки меню", error); }
    };

    const fetchUsersInfo = async () => {
      try {
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams({ collection_code: "vtbl_quasar_user_data" }).toString()
        );
        userArray.value = response.data.results[0];
      } catch (error) { console.error("Ошибка загрузки данных сотрудника", error); }
    };

    const fetchDatePickerInfo = async () => {
      try {
        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams({ collection_code: "VTBLGetEventCalendarEvents" }).toString()
        );
        datePicker.value = response.data.results || [];
      } catch (error) { console.error("Ошибка загрузки календаря", error); }
    };

    const mutObserver = async () => {
      let observer = new MutationObserver(function (mutations) {
        mutations.forEach(function (mutationRecord) {
          document.getElementById("wt-body").style.paddingLeft =
            mutationRecord.target.style.paddingLeft;
        });
      });
      observer.observe(document.getElementsByClassName("q-page-container")[0], {
        attributes: true,
        attributeFilter: ["style"],
      });
    };

    onMounted(async () => {
      await fetchMenuLinks();
      await fetchUsersInfo();
      await fetchDatePickerInfo();
      await mutObserver();
      await nextTick();

      const root = calendarRef.value?.$el;
      if (root) root.addEventListener("mousemove", onCalendarMouseMove);

      const searchQfieldNative = document.querySelectorAll(".q-field__native");
      searchQfieldNative.forEach((el) => { el.style.paddingLeft = "10px"; });

      document.querySelectorAll('[role="listitem"]').forEach((element) => {
        element.addEventListener("mouseenter", () => {
          element.style.backgroundColor = "rgba(255, 255, 255, 0.2)";
        });
        element.addEventListener("mouseleave", () => {
          element.style.backgroundColor = "";
        });
      });
    });

    onUnmounted(() => {
      const root = calendarRef.value?.$el;
      if (root) root.removeEventListener("mousemove", onCalendarMouseMove);
    });

    const getChildLinks = (id) => menuLinks.value.filter((link) => link.parent_id === id);
    const hasChildLinks = (id) => getChildLinks(id).length > 0;

    function toggleLeftDrawer() { leftDrawerOpen.value = !leftDrawerOpen.value; }

    function normalizeText(text) {
      return text.includes("&nbsp;") ? text.replace(/&nbsp;/g, " ") : text;
    }

    function onItemToggle(item, value) {
      openedItem.value = value ? item : "";
    }

    return {
      leftDrawerOpen, searchCatalog, toggleLeftDrawer,
      openedItem, onItemToggle, categories, userArray,
      getChildLinks, normalizeText, hasChildLinks,
      userURL, originURL, calendarRef, selectedDate,
      selectedEvents, tooltip, onDateClick, goToEvent,
      eventDates, getEventColor, getEventsByDate,
    };
  },

  methods: {
    goToFrame(path) { window.location.href = path; },
    onSearchButtonClick() { window.location.href = `search?word=${this.searchCatalog}`; },
    onSearchEnter() { window.location.href = `search?word=${this.searchCatalog}`; },
  },
};
</script>

<!-- Все остальные стили — scoped, без изменений -->
<style scoped>
.calendar-wrapper {
  position: relative;
  display: flex;
  justify-content: center;
  /* overflow: visible — убираем overflow вообще, он здесь не нужен */
}

/* ... остальные scoped стили без изменений ... */

/* Старый .calendar-tooltip отсюда УДАЛЯЕМ — он переехал в глобальный блок ниже */
</style>

<!-- ✅ Отдельный глобальный блок — без scoped.
     Нужен потому что teleport рендерит тултип в <body>,
     вне компонента, и scoped-стили туда не доходят. -->
<style>
.calendar-tooltip {
  position: fixed;                  /* координаты от viewport, а не от родителя */
  transform: translateY(-100%);     /* поднимаем тултип над кнопкой */
  z-index: 99999;
  min-width: 220px;
  max-width: 260px;
  padding: 12px;
  border-radius: 12px;
  background: rgba(35, 35, 35, 0.96);
  color: white;
  backdrop-filter: blur(12px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.25);
  pointer-events: none;
  font-size: 13px;
}

.tooltip-event:not(:last-child) {
  margin-bottom: 10px;
  padding-bottom: 10px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}
</style>
