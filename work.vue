<template>
  <ul class="stages-list">
    <li v-for="task in process.tasks" :key="task.id" class="stages-item">
      <div class="duration-wrapper">
        <div class="process-description-wrapper row">
          <q-icon size="xs" name="fa-solid fa-circle-arrow-right" color="grey" class="process-description-icon" />
          <p class="process-description row">{{ task.description }}</p>
        </div>
      </div>

      <div class="process-links-wrapper row">
        <ul class="process-link-list row">
          <li v-for="(material, materialIndex) in task.materials" :key="materialIndex" class="process-link-item row">
            <a
              v-if="material.materialName !== 'Коллеги, рекомендованные для знакомства'"
              :href="material.materialLink"
              class="process-link"
            >
              {{ material.materialName }}
            </a>
            <span
              v-else
              class="process-link cursor-pointer text-primary"
              @click="openCoworkersModal"
            >
              {{ material.materialName }}
            </span>
          </li>
        </ul>
      </div>
    </li>
  </ul>

  <!-- Модальное окно -->
  <q-dialog v-model="isCoworkersModalOpen">
    <q-card>
      <q-card-section>
        <div class="text-h6">Рекомендованные коллеги</div>
      </q-card-section>

      <q-card-section>
        <ul v-if="coworkersData.length > 0">
          <li v-for="coworker in coworkersData" :key="coworker.id">
            <q-avatar v-if="coworker.avatar" size="40px" class="q-mr-sm">
              <img :src="coworker.avatar" />
            </q-avatar>
            <span>{{ coworker.name }} - {{ coworker.position }}</span>
          </li>
        </ul>
        <p v-else>Нет данных о коллегах.</p>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="Закрыть" color="primary" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
import { ref, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const isCoworkersModalOpen = ref(false);
    const coworkersData = ref([]);

    const openCoworkersModal = async () => {
      isCoworkersModalOpen.value = true;
      await fetchCoworkers();
    };

    const fetchCoworkers = async () => {
      try {
        const params = {
          collection_code: "vtbl_adaptation_2025",
          parameters: "data_mode=meet_collaborator",
        };

        const response = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        coworkersData.value = response.data.results;
      } catch (error) {
        console.error("Ошибка загрузки данных о коллегах", error);
      }
    };

    return {
      isCoworkersModalOpen,
      coworkersData,
      openCoworkersModal,
    };
  },
};
</script>
