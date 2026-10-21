<template>
  <div class="container" style="max-width: 800px">
    <h1 class="text-center my-4">Tarefinhas</h1>

    <TaskStats :tasks="tasks"/>

    <TaskInput @add-task="addTask"/>

    <TaskFilter v-if="tasks.length" v-model:search="filterSearch" v-model:status="filterStatus"/>

    <TaskEmptyState
      v-if="!filteredTasks.length"
      :message="emptyStateMessage"
    />

    <TaskList v-model:tasks="filteredTasks" @on-delete="deleteTask"/>
  </div>
</template>

<script setup>
import { computed, ref } from "vue"
import TaskStats from "./components/TaskStats.vue"
import TaskInput from "./components/TaskInput.vue"
import TaskFilter from "./components/TaskFilter.vue"
import TaskList from "./components/TaskList.vue"
import TaskEmptyState from './components/TaskEmptyState.vue'

const tasks = ref([])
const filterSearch = ref('')
const filterStatus = ref('')

const emptyStateMessage = computed(() => {
  let output = 'Nenhuma tarefa cadastrada.'

  if (filterSearch.value || filterStatus.value) {
    output = 'Nenhum resultado para este filtro.'
  }

  return output
})

const filteredTasks = computed(() => {
  let output = tasks.value
  if (filterSearch.value) {
    const searchText = filterSearch.value.toLowerCase()
    output = output.filter(o => o.name.toLowerCase().includes(searchText))
  }

  if (filterStatus.value === 'pending') {
    output = output.filter(o => o.completed === false)
  } else if (filterStatus.value === 'completed') {
    output = output.filter(o => o.completed === true)
  }

  return output
})

function addTask(task) {
  if (!task) return

  tasks.value.push({
    id: Date.now(),
    name: task,
    completed: false,
    state: 'show'
  })
}

function deleteTask(task) {
  const index = tasks.value.findIndex(o => o.id === task.id)
  if (index !== -1) {
    tasks.value.splice(index, 1)
  }
}

</script>
