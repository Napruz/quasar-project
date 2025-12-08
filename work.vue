<!-- Диалоговое окно выбора сотрудника -->
<q-dialog v-model="collaboratorDialogOpen">
  <q-card style="min-width:400px; max-width:600px;">
    
    <q-card-section>
      <div class="text-h6">Выбор сотрудника</div>
    </q-card-section>

    <q-card-section class="q-pt-none">

      <!-- Поиск -->
      <div class="row q-col-gutter-md q-mb-sm">
        <div class="col">
          <q-input
            v-model="collaboratorSearch"
            placeholder="Поиск сотрудника..."
            dense outlined
            @input="debouncedSearch"
          />
        </div>
        <div>
          <q-btn color="primary" label="Найти" @click="fetchCollaboratorSearch" />
        </div>
      </div>

      <!-- Список сотрудников -->
      <q-list bordered separator>
        <q-item
          v-for="item in collaboratorListData"
          :key="item.person_id"
          clickable
          @click="selectCollaborator(item)"
        >
          <q-item-section>{{ item.fullname }}</q-item-section>
        </q-item>
      </q-list>

      <!-- Переключение страниц -->
      <div class="row justify-between items-center q-mt-sm">
        <q-btn flat label="Назад"
               :disable="collaboratorPage <= 1"
               @click="prevPage" />

        <q-btn flat label="Вперёд"
               @click="nextPage" />
      </div>

    </q-card-section>

    <q-card-actions align="right">
      <q-btn flat label="Закрыть" v-close-popup />
    </q-card-actions>

  </q-card>
</q-dialog>

<script>
import { ref, watch } from "vue";
import axios from "axios";

export default {
  setup() {
    const collaboratorDialogOpen = ref(false);
    const collaboratorSearch = ref("");
    const collaboratorListData = ref([]);
    const collaboratorPage = ref(1);

    const BACKEND_URL = "...";
    const wtSecId = "...";

    let searchTimeout = null;

    /* --------------------- ДЕБАУНС ---------------------- */
    const debouncedSearch = () => {
      clearTimeout(searchTimeout);
      searchTimeout = setTimeout(() => {
        fetchCollaboratorSearch();
      }, 300);
    };

    /* --------------------- СБРОС ПРИ ЗАКРЫТИИ ---------------------- */
    const resetCollaboratorSearch = () => {
      collaboratorSearch.value = "";
      collaboratorPage.value = 1;
      collaboratorListData.value = [];
      clearTimeout(searchTimeout);
    };

    watch(collaboratorDialogOpen, (val) => {
      if (!val) resetCollaboratorSearch();
    });

    /* --------------------- API ---------------------- */
    const fetchCollaboratorList = async () => {
      try {
        const pageSize = 10;
        const start = (collaboratorPage.value - 1) * pageSize;

        const params = {
          collection_code: "vtbl_adaptation_manager_2025",
          secid: wtSecId,
          limit: pageSize,
          page: collaboratorPage.value,
          start,
          parameters: `data_mode=collaborator_list;search_str=${collaboratorSearch.value};secid=${wtSecId}`
        };

        const resp = await axios.post(
          BACKEND_URL,
          new URLSearchParams(params).toString()
        );

        collaboratorListData.value = resp.data.results || [];

      } catch (err) {
        console.error("Ошибка загрузки списка:", err);
      }
    };

    const fetchCollaboratorSearch = async () => {
      collaboratorPage.value = 1;
      await fetchCollaboratorList();
    };

    /* --------------------- СТРАНИЦЫ ---------------------- */
    const nextPage = async () => {
      collaboratorPage.value++;
      await fetchCollaboratorList();
    };

    const prevPage = async () => {
      if (collaboratorPage.value > 1) {
        collaboratorPage.value--;
        await fetchCollaboratorList();
      }
    };

    /* --------------------- ВЫБОР СОТРУДНИКА ---------------------- */
    const selectedCollaborator = ref([]);

    const selectCollaborator = (item) => {
      if (item.email && !selectedCollaborator.value.includes(item.email)) {
        selectedCollaborator.value.push(item.email);
      }
      collaboratorDialogOpen.value = false; // закрываем окно
    };

    return {
      collaboratorDialogOpen,
      collaboratorSearch,
      collaboratorListData,
      collaboratorPage,

      debouncedSearch,
      fetchCollaboratorSearch,
      nextPage,
      prevPage,

      selectCollaborator
    };
  }
}
</script>
