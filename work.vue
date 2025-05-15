const fetchUsersData = async () => {
  try {
    const adaptationId = route.params.id;

    const params = {
      collection_code: "vtbl_adaptation_manager_2025",
      parameters: `data_mode=get_adaptation_info;adaptation_id=${adaptationId}`,
    };

    const response = await axios.post(
      BACKEND_URL,
      new URLSearchParams(params).toString()
    );

    const result = response?.data?.results?.[0];

    if (!result) {
      console.warn("Пустой ответ от сервера: results[0] отсутствует");
      usersData.value = {};
      return;
    }

    usersData.value = result;

    personFullname.value = result.person?.person_fullname || "";
    mentorFullname.value = result.mentor?.person_fullname || "";
    userEventsLentgh.value = result.events?.length || 0;

    userSecondStage.value = result.stages?.[1]?.percent || 0;
    userThirdStage.value = result.stages?.[2]?.percent || 0;
  } catch (error) {
    console.error("Ошибка загрузки кабинета", error);
  }
};

<!-- Оберни весь блок в v-if -->
<div class="progress-bar-container" v-if="usersData && usersData.person">

