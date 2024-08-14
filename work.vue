methods: {
  goToFrame(id) {
    const encodedId = encodeURIComponent(id);
    this.$router.push(`/frame/${encodedId}`);
  }
}

<template>
  <iframe
    class="main-frame"
    :src="frameSrc"  // динамическое значение src
    scrolling="no"
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
  }
}
</script>

<style scoped>
.main-frame {
  margin: 0 auto;
  width: 100%;
  height: 1800px;
}
</style>
