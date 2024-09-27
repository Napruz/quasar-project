<ul class="courses-themes-list">
  <li
    v-for="(course, index) in category.courses.filter(c => c !== '')"
    :key="index"
    class="list-item"
  >
    <div class="themes-arrow-items row">
      <div class="q-ml-xs">
        <p class="main-themes-description text-black q-mb-xs">
          {{ course }}
        </p>
      </div>
    </div>
  </li>
</ul>
