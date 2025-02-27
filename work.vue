methods: {
  printPage() {
    const drawer = document.querySelector('.q-drawer')

    if (drawer) {
      drawer.style.display = 'none'
    }

    setTimeout(() => {
      window.print()

      // Вернем меню после печати
      setTimeout(() => {
        if (drawer) drawer.style.display = ''
      }, 500)
    }, 300) // Подбираем задержку под твою страницу
  }
}
