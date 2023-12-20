<script setup lang="ts">
import {ref} from "vue";

const listsData = ref([
  {id: 1, title: 'Назначено', tasks: [{id: 1, task: 'Исправть баг'}, {id: 2, task: 'Добавить таблицу'}]},
  {id: 2, title: 'В работе', tasks: [{id: 3, task: 'Добавить фон'}, {id: 4, task: 'Перекрасить таблицу'}]},
  {id: 3, title: 'Тестирование', tasks: [{id: 5, task: 'Сверстать блок'}, {id: 6, task: 'Добавить значения в список'}]},
  {id: 4, title: 'Готово', tasks: [{id: 7, task: 'Скачать в PDF'}, {id: 8, task: 'Удалить фильтр по дате'}]},
])

const listIdStartDrag = ref<null | number>(null)

const drop = (event: DragEvent, list: any) => {

  const taskId = event.dataTransfer?.getData('itemId')
  const processStartDrag = listsData.value.find(process => process.id === listIdStartDrag.value).tasks
  const task = processStartDrag.find(task => task.id == taskId)
  const taskIdx = processStartDrag.findIndex(task => task.id == taskId)

  processStartDrag.splice(taskIdx, 1)

  const processEndDrag = listsData.value.find(process => process.id === list.id).tasks
  const draggableTitle = event.target.value
  const taskDropIdx = processEndDrag.findIndex(task => task.title == draggableTitle)

  processEndDrag.splice(taskDropIdx + 1,0,task)

  document.querySelectorAll('.list__task').forEach(item => {
    item.style.boxShadow = ''
  })

  document.querySelectorAll('.list').forEach(item => {
    item.style.backgroundColor = '';

    if (event.target.className === 'list') {
      event.target.style.backgroundColor = 'rgba(162, 159, 159, 0.2)';
    } else {
      event.target.parentElement.style.backgroundColor = 'rgba(162, 159, 159, 0.2)';
    }
  });
}

const dragStart = (event: DragEvent, taskId: any, list: any) => {
  event.dataTransfer?.setData('itemId', taskId.id)
  listIdStartDrag.value = list.id
}

const dragOver = (event: DragEvent) => {
  if (event.target.className == 'list__task') {
    event.target.style.boxShadow = '0 4px 4px gray'
  }
}

const dragLeave = (event: DragEvent) => {
  if (event.target.className == 'list__task') {
    event.target.style.boxShadow = ''
  }
}

const dragOverBox = (event: DragEvent) => {
  if (event.target.className == 'list') {
    event.target.style.backgroundColor = 'rgb(162, 159, 159, 0.2)'
  }
}

const dragLeaveBox = (event: DragEvent) => {
  if (event.target.className == 'list') {
    if (!event.target.contains(event.relatedTarget as Node)) {
      event.target.style.backgroundColor = '';
    }
  }
}

</script>

<template>
  <div class="lists">
    <div
        class="list" v-for="list in listsData"
        key="list.id"
        @drop.prevent="drop($event, list)"
        @dragover.prevent="dragOverBox($event)"
        @dragleave="dragLeaveBox($event)"
    >
      <div class="list__title">{{ list.title }}</div>
      <div
          class="list__task"
          v-for="task in list.tasks"
          key="task.id"
          draggable="true"
          @dragstart="dragStart($event, task, list)"
          @dragover.prevent="dragOver($event)"
          @dragleave="dragLeave($event)"
      >
        {{ task.task }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.lists {
  padding-top: 3rem;
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.list {
  padding: 1rem;
  border: 3px solid gray;
  border-radius: 0.5rem;
  min-width: 200px;
}

.list__title {
  font-size: 1.5rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.list__task {
  padding: 0.5rem;
  border-radius: 0.2rem;
  border: 1px solid #e36319;
  background-color: rgba(227, 99, 25, 0.2);
  margin-bottom: 0.5rem;
  cursor: grab;
}

</style>
