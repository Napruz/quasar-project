<a
  v-if="category.buttons[0].command === 'start'"
  :href="category.buttons[0].url"
  class="start-course-link"
  style="text-decoration: none; color: white; padding: 4px 8px; background-color: #3f51b5; border-radius: 4px; display: inline-block;"
>
  Назначить курс
</a>
<a
  v-if="category.buttons[0].command === 'continue'"
  :href="category.buttons[0].url"
  class="continue-course-link"
  style="text-decoration: none; color: white; padding: 4px 8px; background-color: #3f51b5; border-radius: 4px; display: inline-block;"
>
  Продолжить курс
</a>
.start-course-link,
.continue-course-link {
  text-decoration: none;
  color: white;
  padding: 4px 8px;
  background-color: #3f51b5;
  border-radius: 4px;
  display: inline-block;
}

.start-course-link:hover,
.continue-course-link:hover {
  background-color: #303f9f;
}
