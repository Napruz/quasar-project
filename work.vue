<q-btn
  flat
  round
  color="primary"
  padding="none"
  size="md"
  :icon="activeBanner === category.id ? 'mdi-chevron-up' : 'mdi-chevron-down'" <!-- Меняем иконку -->
  :class="{'transform-button': activeBanner === category.id}" <!-- Добавляем класс при активном баннере -->
  @click="toggleBanner(category.id)" <!-- Обработчик клика -->
/>
