methods: {
  printPage() {
    const drawer = document.querySelector('.q-drawer');
    if (drawer) drawer.style.display = 'none';

    window.print();

    setTimeout(() => {
      if (drawer) drawer.style.display = '';
    }, 1000);
  }
}
