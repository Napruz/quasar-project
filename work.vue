// Используем onMounted для инициализации MutationObserver
    onMounted(() => {
      fetchMenuLinks();
      fetchUsersInfo();

      // Определяем функцию для поиска элемента и добавления стилей
      const addHoverEffect = () => {
        const targetElement = document.querySelector(".q-item.q-item-type.row.no-wrap");
        if (targetElement) {
          targetElement.classList.add("hover-effect");
        }
      };

      // Инициализируем MutationObserver
      const observer = new MutationObserver((mutationsList, observer) => {
        for (let mutation of mutationsList) {
          if (mutation.type === "childList") {
            addHoverEffect();
          }
        }
      });

      // Начинаем наблюдение за изменениями в DOM
      observer.observe(document.body, { childList: true, subtree: true });

      // Пытаемся добавить эффект hover сразу
      addHoverEffect();
    });

