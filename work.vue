<q-dialog
  v-model="collaboratorDialogOpen"
  @hide="onCollaboratorDialogHide"
>
  ...
</q-dialog>

import { ref } from 'vue';

export default {
  setup() {
    const collaboratorDialogOpen = ref(false);
    const collaboratorSearch = ref('');
    const collaboratorPage = ref(1);
    const collaboratorListData = ref([]);

    const resetCollaboratorSearch = () => {
      collaboratorSearch.value = '';
      collaboratorPage.value = 1;
      collaboratorListData.value = [];
    };

    const onCollaboratorDialogHide = () => {
      // вызывется всегда при закрытии (клик вне, esc, programmatic toggle)
      resetCollaboratorSearch();
    };

    return {
      collaboratorDialogOpen,
      collaboratorSearch,
      collaboratorPage,
      collaboratorListData,
      onCollaboratorDialogHide,
    };
  }
};

