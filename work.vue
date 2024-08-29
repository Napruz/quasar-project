<template>
  <q-expansion-item
    v-for="category in categories"
    :key="category.id"
    :icon="category.icon"
    icon-class="text-white"
    label-class="text-white"
    expand-icon-class="text-white"
    expand-icon-toggle
    class="text-white link-text"
    :model-value="openedItem === category.id"
    :hide-expand-icon="!hasChildLinks(category.id)"
    @update:model-value="(value) => onItemToggle(category.id, value)"
  >
    <!-- Заголовок сохраняет стили и поведение -->
    <template v-slot:header>
      <div
        class="row items-center q-gutter-md cursor-pointer"
        style="margin-right: auto;"
        @click.stop="goToFrame(category.path)"
      >
        <q-icon :name="category.icon" class="text-white" />
        <span class="text-white">{{ removeNbsp(category.text) }}</span>
      </div>
    </template>

    <!-- Дочерние элементы -->
    <q-item
      v-for="link in getChildLinks(category.id)"
      :key="link.id"
      clickable
      v-ripple
      @click="goToFrame(link.path)"
      class="q-pl-lg"
    >
      <q-item-section avatar>
        <q-icon :name="link.icon" class="text-white q-pa-none" />
      </q-item-section>
      <q-item-section class="text-white link-text">
        {{ link.text }}
      </q-item-section>
    </q-item>
  </q-expansion-item>
</template>

<style>
/* Добавляем эффект hover для родительского элемента с ролью listitem */
.q-item[role="listitem"] {
  transition: background-color 0.3s;
}

.q-item[role="listitem"]:hover {
  background-color: rgba(255, 255, 255, 0.1); /* Цвет фона при наведении */
}

/* Если необходимо учитывать активный элемент */
.q-item[role="listitem"].q-item--active {
  background-color: rgba(255, 255, 255, 0.2); /* Цвет фона для активного элемента */
}
</style>

