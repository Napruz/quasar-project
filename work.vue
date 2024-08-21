<template>
  <iframe
    class="main-frame"
    :src="frameSrc"
    scrolling="no"
    @load="clearFrame" <!-- Привязка события загрузки -->
  ></iframe>
</template>

<script>
export default {
  computed: {
    frameSrc() {
      const encodedId = this.$route.params.id;  // получаем закодированный id из маршрута
      const id = decodeURIComponent(encodedId);  // декодируем его обратно в оригинальный URL
      return id;  // используем декодированный URL для генерации src
    }
  },
  methods: {
    clearFrame() {
      const iframe = document.getElementsByClassName('main-frame')[0];  // Получаем iframe
      const contentDocument = iframe.contentDocument || iframe.contentWindow.document;  // Получаем документ внутри iframe

      if (contentDocument) {
        // Убираем отступы и скрываем шапку
        const page = contentDocument.getElementsByClassName('page')[0];
        const header = contentDocument.getElementsByClassName('header')[0];

        if (page) {
          page.setAttribute('style', 'padding-top:0px');
        }

        if (header) {
          header.setAttribute('style', 'display:none');
        }
      }
    }
  }
}
</script>
