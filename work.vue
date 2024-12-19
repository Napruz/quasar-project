mounted() {
  console.log('Component mounted'); // Убедитесь, что метод вызван.
  this.fetchEventData();

  nextTick(() => {
    const buttonMargin = document.querySelectorAll('.block');
    console.log('Elements found:', buttonMargin.length); // Убедитесь, что элементы найдены.
    buttonMargin.forEach((el) => {
      el.style.marginTop = "2px";
      console.log('button', el); // Выведите элемент для проверки.
    });
  });
},
