methods: {
  navigateToTab(tabId) {
    const path = `/content-items/${tabId}`;
    this.$router.push(path);
  }
}

<q-tabs
  v-model="tab"
  class="text-primary q-mb-sm"
  active-color="secondary"
  align="justify"
  narrow-indicator
>
  <q-tab 
    name="7207783892693892797" 
    label="Профессиональные навыки"
    @click="navigateToTab('7207783892693892797')"
  />
  <q-tab 
    name="7207784069377055908" 
    label="Личная эффективность"
    @click="navigateToTab('7207784069377055908')"
  />
  <q-tab 
    name="7207784147986504834" 
    label="Вводные курсы"
    @click="navigateToTab('7207784147986504834')"
  />
  <q-tab 
    name="7313878051766677160" 
    label="УЭД"
    @click="navigateToTab('7313878051766677160')"
  />
</q-tabs>

