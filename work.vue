function addStyleToDiv() {
  // Находим div по определенному критерию (ID, класс, селектор)
  const targetDiv = document.querySelector('#myDiv'); // пример с ID

  // Проверяем, найден ли div
  if (targetDiv) {
    // Создаем стиль (в виде строки)
    const style = `
      background-color: #f0f0f0;
      color: #333;
      padding: 10px;
      border-radius: 5px;
    `;

    // Добавляем стиль к элементу
    targetDiv.style.cssText = style;
  } else {
    console.error('Не удалось найти div с ID #myDiv');
  }
}

// Вызываем функцию после загрузки страницы
window.addEventListener('DOMContentLoaded', addStyleToDiv); 
