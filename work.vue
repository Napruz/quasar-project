function shortenString(inputString) {
  // Регулярное выражение: ищет текст в скобках
  const regex = /\(([^)]+)\)/;
  const match = inputString.match(regex);

  // Если сокращение найдено
  if (match) {
    return match[1]; // Возвращает найденное сокращение
  } else {
    // Если сокращения не найдено
    return inputString;
  }
}

// Пример использования
const originalString = "УПРАВЛЕНИЕ ЭФФЕКТИВНОСТЬЮ ДЕЯТЕЛЬНОСТИ (УЭД)";
const shortenedString = shortenString(originalString);
console.log(shortenedString); // Вывод: УЭД
