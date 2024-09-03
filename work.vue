function shortenString(inputString) {
  // Регулярное выражение: ищет текст в скобках
  const regex = /\(([^)]+)\)/;
  const match = inputString.match(regex);

  // Если сокращение найдено
  if (match) {
    // Разделяем строку по пробелам и проверяем, есть ли слово "ВВОДНЫЕ" 
    const words = inputString.split(' ');
    if (words.includes("ВВОДНЫЕ")) {
      return words.slice(0, words.indexOf('(')).join(' '); // Возвращаем слова до скобки
    } else {
      return match[1]; // Возвращаем найденное сокращение
    }
  } else {
    // Если сокращения не найдено
    return inputString;
  }
}

// Пример использования
const originalString1 = "УПРАВЛЕНИЕ ЭФФЕКТИВНОСТЬЮ ДЕЯТЕЛЬНОСТИ (УЭД)";
const originalString2 = "ВВОДНЫЕ КУРСЫ (ИНСТРУКТАЖИ)";

const shortenedString1 = shortenString(originalString1);
const shortenedString2 = shortenString(originalString2);

console.log(shortenedString1); // Вывод: УЭД
console.log(shortenedString2); // Вывод: ВВОДНЫЕ КУРСЫ
