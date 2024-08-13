<template>
  <q-expansion-item
    v-for="category in categories"
    :key="category.id"
    :icon="category.icon"
    :label="removeNbsp(category.text)"
    class="text-white link-text"
    label-class="text-white"
    icon-class="text-white"
    :hide-expand-icon="!hasChildren(category.id)" 
    :model-value="openedItem === category.id"
    @update:model-value="(value) => onItemToggle(category.id, value)"
  >
    <!-- ... (остальное содержимое q-expansion-item) ... -->
  </q-expansion-item>
</template>

<script>
// ... (другой код) ...

setup() {
  // ... (другие переменные) ...

  const hasChildren = (categoryId) => {
    return categories.value.some((category) => category.parent_id === categoryId);
  };

  // ... (остальной код) ...
}
</script>
