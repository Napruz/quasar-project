const calendarRef = ref(null);

const onCalendarMouseMove = (e) => {
  const root = calendarRef.value?.$el;
  if (!root) return;

  const btn = e.target.closest(
    ".q-date__calendar-days button"
  );

  if (!btn) {
    tooltip.value.visible = false;
    return;
  }

  const dot = btn.querySelector(".q-date__event");
  if (!dot) {
    tooltip.value.visible = false;
    return;
  }

  const day = btn.querySelector(".block")?.textContent?.trim();
  if (!day) return;

  const displayed = getDisplayedMonthYear();
  if (!displayed) return;

  const date =
    `${displayed.year}-${displayed.month}-${day.padStart(2, "0")}`;

  const events = getEventsByDate(date);
  if (!events.length) {
    tooltip.value.visible = false;
    return;
  }

  const rect = btn.getBoundingClientRect();
  const wrapper = root
    .closest(".calendar-wrapper")
    ?.getBoundingClientRect();

  tooltip.value = {
    visible: true,
    x: rect.left - wrapper.left + rect.width / 2,
    y: rect.top - wrapper.top - 10,
    events,
  };
};

onMounted(async () => {
  await fetchDatePickerInfo();
  await nextTick();

  const root = calendarRef.value?.$el;
  if (root) {
    root.addEventListener("mousemove", onCalendarMouseMove);
  }
});

onUnmounted(() => {
  const root = calendarRef.value?.$el;
  if (root) {
    root.removeEventListener("mousemove", onCalendarMouseMove);
  }
});
