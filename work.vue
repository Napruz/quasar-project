// Обработчик на иконку в поле поиска
const onSearchButtonClick = () => {
  window.location.href = `/search?word=${encodeURIComponent(search.value)}`;
};

// Обработчик Enter в поле поиска
const onSearchEnter = (event) => {
  if (event.key === "Enter") {
    window.location.href = `/search?word=${encodeURIComponent(search.value)}`;
  }
};
