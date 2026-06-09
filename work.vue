<q-date
  ref="calendarRef"
  v-model="selectedDate"
  flat
  bordered
  minimal
  mask="YYYY-MM-DD"
  color="primary"
  class="custom-calendar"
  :events="eventDates"
  :event-color="getEventColor"
  @update:model-value="onDateClick"
/>

import {
  ref,
  onMounted,
  onUnmounted,
  nextTick,
  computed
} from "vue";

const calendarRef = ref(null);
let calendarObserver = null;

const monthMap = {
  Январь: "01",
  Февраль: "02",
  Март: "03",
  Апрель: "04",
  Май: "05",
  Июнь: "06",
  Июль: "07",
  Август: "08",
  Сентябрь: "09",
  Октябрь: "10",
  Ноябрь: "11",
  Декабрь: "12",
};

const getDisplayedMonthYear = () => {
  const root =
    calendarRef.value?.$el ||
    calendarRef.value;

  if (!root) return null;

  const blocks = root.querySelectorAll(
    ".q-date__navigation .block"
  );

  if (blocks.length < 2) return null;

  return {
    month: monthMap[
      blocks[0].textContent.trim()
    ],
    year: blocks[1].textContent.trim(),
  };
};

const bindCalendarTooltips = async () => {
  await nextTick();

  const root =
    calendarRef.value?.$el ||
    calendarRef.value;

  if (!root) return;

  const buttons = root.querySelectorAll(
    ".q-date__calendar-days button"
  );

  buttons.forEach((btn) => {
    if (btn.dataset.tooltipBound) return;

    const eventDot =
      btn.querySelector(".q-date__event");

    if (!eventDot) return;

    btn.dataset.tooltipBound = "1";

    btn.addEventListener("mouseenter", () => {
      const day = btn
        .querySelector(".block")
        ?.textContent?.trim();

      if (!day) return;

      const displayed =
        getDisplayedMonthYear();

      if (!displayed) return;

      const date =
        `${displayed.year}-${displayed.month}-${day.padStart(
          2,
          "0"
        )}`;

      const events =
        getEventsByDate(date);

      if (!events.length) return;

      const rect =
        btn.getBoundingClientRect();

      const wrapper =
        root
          .closest(".calendar-wrapper")
          .getBoundingClientRect();

      tooltip.value = {
        visible: true,
        x:
          rect.left -
          wrapper.left +
          rect.width / 2,
        y:
          rect.top -
          wrapper.top -
          10,
        events,
      };
    });

    btn.addEventListener("mouseleave", () => {
      tooltip.value.visible = false;
    });
  });
};

const initCalendarObserver = () => {
  const root =
    calendarRef.value?.$el ||
    calendarRef.value;

  if (!root) return;

  const daysContainer =
    root.querySelector(
      ".q-date__calendar-days"
    );

  if (!daysContainer) return;

  calendarObserver =
    new MutationObserver(() => {
      bindCalendarTooltips();
    });

  calendarObserver.observe(
    daysContainer,
    {
      childList: true,
      subtree: true,
    }
  );
};

onMounted(async () => {
  await fetchDatePickerInfo();

  await nextTick();

  bindCalendarTooltips();

  initCalendarObserver();
});

  onUnmounted(() => {
  calendarObserver?.disconnect();
});

    return {
  calendarRef,
  selectedDate,
  selectedEvents,
  tooltip,
  onDateClick,
  goToEvent,
  eventDates,
  getEventColor,
  getEventsByDate,
  hasEvents,
};

    .calendar-tooltip {
  pointer-events: none;
}
