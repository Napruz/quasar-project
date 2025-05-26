const isEmployeeTask = (process, task) => {
  if (process.title === 'Программа обучения') {
    return task.description !== 'Изучите необходимые документы и материалы';
  }
  return false;
};

<!-- Автор действия -->
<div
  class="manager-title"
  style="
    margin-left: 10px;
    background-color: #ddd;
    border-radius: 10px;
    width: fit-content;
    padding: 0 5px;
  "
>
  <q-icon
    :color="process.title === 'Программа обучения' 
              ? (isEmployeeTask(process, task) ? 'blue' : 'red')
              : 'red'"
    size="8px"
    name="fa-solid fa-circle"
  />

  <span
    class="manager-title-item"
    style="margin-left: 5px; font-size: 12px;"
  >
    {{ process.title === 'Программа обучения'
        ? (isEmployeeTask(process, task) ? 'Сотрудник' : 'Руководитель')
        : 'Руководитель' }}
  </span>
</div>

<q-toggle
  :disable="true"
  v-model="isEmployeeTask(process, task) ? task.completedCollaborator : task.completedMentor"
  :label="(isEmployeeTask(process, task) ? task.completedCollaborator : task.completedMentor) ? 'Выполнено' : 'Не выполнено'"
  color="green"
  unchecked-color="red"
>
  <q-tooltip>
    {{
      isEmployeeTask(process, task)
        ? 'Данное поле заполняется сотрудником'
        : 'Задача сохраняется автоматически'
    }}
  </q-tooltip>
</q-toggle>


Всё вместе
<div v-if="process.title === 'Программа обучения'">
  <q-icon
    :color="isEmployeeTask(process, task) ? 'blue' : 'red'"
    size="8px"
    name="fa-solid fa-circle"
  />
  <span class="manager-title-item">
    {{ isEmployeeTask(process, task) ? 'Сотрудник' : 'Руководитель' }}
  </span>

  <q-toggle
    :disable="true"
    v-model="isEmployeeTask(process, task) ? task.completedCollaborator : task.completedMentor"
    :label="(isEmployeeTask(process, task) ? task.completedCollaborator : task.completedMentor) ? 'Выполнено' : 'Не выполнено'"
    color="green"
    unchecked-color="red"
  >
    <q-tooltip>
      {{
        isEmployeeTask(process, task)
          ? 'Данное поле заполняется сотрудником'
          : 'Задача сохраняется автоматически'
      }}
    </q-tooltip>
  </q-toggle>
</div>
