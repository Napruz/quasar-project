const rect = btn.getBoundingClientRect();
const wrapper = root.closest('.calendar-wrapper')?.getBoundingClientRect();
let left = rect.right - wrapper.left + 12;
let top = rect.top - wrapper.top + rect.height / 2;

// Дополнительная проверка: если тултип выходит за правую границу wrapper'а
// можно сдвинуть его влево (показать слева от кнопки)
tooltip.value = {
  x: left,
  y: top,
  events,
};
