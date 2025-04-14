setup() {
  const underGoingAdaptation = ref([]);
  const preparingForReception = ref([]);
  const completedAdaptation = ref([]);
  const wasFiredOnAdaptation = ref([]);

  const expansionStates = ref({
    undergoing: false,
    preparing: false,
    completed: false,
    fired: false,
  });

  const saveExpansionState = () => {
    sessionStorage.setItem('expansionStates', JSON.stringify(expansionStates.value));
  };

  const loadExpansionState = () => {
    const saved = sessionStorage.getItem('expansionStates');
    if (saved) {
      expansionStates.value = JSON.parse(saved);
    }
  };

  const fetchUsersData = async () => {
    // ... твой код
  };

  onMounted(() => {
    loadExpansionState();
    fetchUsersData();
  });

  return {
    underGoingAdaptation,
    preparingForReception,
    completedAdaptation,
    wasFiredOnAdaptation,
    expansionStates,
    saveExpansionState,
  };
}
