function shortenString(inputString) {
  // Разделяем строку на слова
  const words = inputString.split(' ');

  // Проверяем, есть ли в строке сокращение в скобках
  const abbreviation = words.find(word => word.startsWith('(') && word.endsWith(')'));

  // Если сокращение найдено, возвращаем его
  if (abbreviation) {
    return abbreviation.slice(1, -1); // Удаляем скобки
  } else {
    // Если сокращения нет, возвращаем исходную строку
    return inputString;
  }
}

// Пример использования
const originalString = "УПРАВЛЕНИЕ ЭФФЕКТИВНОСТЬЮ ДЕЯТЕЛЬНОСТИ (УЭД)";
const shortenedString = shortenString(originalString);
console.log(shortenedString); // Вывод: УЭД
