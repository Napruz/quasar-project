<style>
.hover-effect:hover {
  background-color: rgba(0, 0, 0, 0.1); 
}
</style>

onMounted(() => {
      fetchMenuLinks();
      fetchUsersInfo();

      // Находим элемент и добавляем ему класс hover-effect
      const targetElement = document.querySelector(".q-item.q-item-type.row.no-wrap");
      if (targetElement) {
        targetElement.classList.add("hover-effect");
      }
    });
