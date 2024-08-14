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
      const id = this.$route.params.id;  // получаем id из маршрута
      return `${id}`;  // используем id для генерации URL
    }
  }
}
</script>

