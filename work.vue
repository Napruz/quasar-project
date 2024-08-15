methods: {
  navigateToTab(tabId) {
    const path = `/content-items/${tabId}`;
    
    // Осуществляем переход на новый маршрут
    this.$router.push(path).then(() => {
      // После успешного перехода перезагружаем страницу
      window.location.reload();
    }).catch(err => {
      console.error('Error navigating to tab:', err);
    });
  }
}
