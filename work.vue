const onCalendarMouseMove = (e) => {
  const root = calendarRef.value?.$el;
  if (!root) return;
  const btn = e.target.closest('.q-date__calendar-days button');
  if (!btn) {
    tooltip.value.visible = false;
    return;
  }
  const dot = btn.querySelector('.q-date__event');
  if (!dot) {
    tooltip.value.visible = false;
    return;
  }
  const day = btn.querySelector('.block')?.textContent?.trim();
  if (!day) return;
  const displayed = getDisplayedMonthYear();
  if (!displayed) return;
  const date = `${displayed.year}-${displayed.month}-${day.padStart(2, '0')}`;
  const events = getEventsByDate(date);
  if (!events.length) {
    tooltip.value.visible = false;
    return;
  }

  const rect = btn.getBoundingClientRect(); // координаты кнопки относительно окна
  tooltip.value = {
    visible: true,
    x: rect.left + rect.width / 2,   // центр кнопки по X
    y: rect.top - 10,                // чуть выше кнопки
    events,
  };
};
