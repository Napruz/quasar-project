const submitComment = () => {
  if (!newComment.value.text.trim() || newComment.value.text.length > 1000) return;

  const now = new Date();
  const formattedDate = `${String(now.getDate()).padStart(2, "0")}.${String(now.getMonth() + 1).padStart(2, "0")}.${now.getFullYear()} ${String(now.getHours()).padStart(2, "0")}:${String(now.getMinutes()).padStart(2, "0")}:${String(now.getSeconds()).padStart(2, "0")}`;

  commentListData.value.unshift({
    id: commentListData.value.length + 1,
    avatar: "#",
    role: "Менеджер",
    name: "Тест Г.О.",
    department: "HR",
    date: formattedDate, // <-- Дата в нужном формате
    text: newComment.value.text,
  });

  postCommentState(newComment.value.text);
  newComment.value.text = "";
};
