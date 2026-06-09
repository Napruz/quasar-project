const onCalendarMouseMove = (e) => {
  const root = calendarRef.value?.$el;
  if (!root) return;

  const btn = e.target.closest(
    ".q-date__calendar-days button"
  );

  // если не над кнопкой дня — скрываем tooltip
  if (!btn) {
    tooltip.value.visible = false;
    return;
  }

  const dot = btn.querySelector(".q-date__event");

  // показываем только дни с событиями
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

  if (!wrapper) return;

  // =========================
  // SMART POSITIONING
  // =========================

  const TOOLTIP_WIDTH = 240;
  const OFFSET = 10;

  let x =
    rect.left -
    wrapper.left +
    rect.width / 2;

  let y =
    rect.top -
    wrapper.top -
    OFFSET;

  // 🔥 если вылезает слева
  if (x - TOOLTIP_WIDTH / 2 < 0) {
    x = TOOLTIP_WIDTH / 2 + OFFSET;
  }

  // 🔥 если вылезает справа
  if (x + TOOLTIP_WIDTH / 2 > wrapper.width) {
    x =
      wrapper.width -
      TOOLTIP_WIDTH / 2 -
      OFFSET;
  }

  // 🔥 если вылезает сверху — показываем снизу
  if (y < 0) {
    y = rect.bottom - wrapper.top + OFFSET;
  }

  tooltip.value = {
    visible: true,
    x,
    y,
    events,
  };
};
