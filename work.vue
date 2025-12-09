watch(collaboratorDialogOpen, (val) => {
  if (val === false) {
    // диалог закрыли (клик снаружи, esc, кнопка)
    resetCollaboratorSearch();
  }
});
