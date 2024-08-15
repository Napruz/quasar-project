methods: {
  handleTabClick(path) {
    if (path.startsWith('http')) {
      // Переход на внешний URL
      window.location.href = path;
    } else {
      // Переход внутри приложения
      this.$router.push(path);
    }
  },
  // ... остальные методы
}
