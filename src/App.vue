<template>
  <div class="container" style="max-width: 800px">
    <h1 class="text-center my-4">Tarefinhas</h1>

    <div class="input-group mb-3">
      <input
        type="text"
        placeholder="Adicionar uma nova tarefa..."
        class="form-control"
        v-model="newTask"
        @keyup.enter="addTask"
      />
      <button
        class="btn btn-success"
        @click="addTask"
      >Adicionar</button>
    </div>

     <div
      class="d-flex gap-2 mb-3"
      v-if="tasks.length"
     >
      <input
        type="text"
        placeholder="Buscar tarefa..."
        class="form-control"
        style="flex: 1;"
        v-model="filterSearch"
      >
      <select
        class="form-select"
        style="flex: 1;"
        v-model="filterStatus"
      >
        <option value="">Todas</option>
        <option value="pending">Pendentes</option>
        <option value="completed">Concluídas</option>
      </select>
      <button
        class="btn btn-outline-secondary btn-sm"
        style="flex-shrink: 0;"
        @click="clearFilters"
      >Limpar filtros</button>
    </div>

     <div v-if="!filteredTasks.length" class="card bg-light">
      <div class="card-body text-center py-5">
        <p class="text-muted mb-0">{{emptyStateMessage}}</p>
      </div>
    </div>

     <ul class="list-group">
      <li
        v-for="task in filteredTasks"
        :key="task.id"
        class="list-group-item d-flex align-items-center gap-2"
      >
        <template v-if="task.state === 'show'">
          <input
            v-model="task.completed"
            type="checkbox"
            class="form-check-input"
          >
          <span
            class="flex-grow-1"
            :class="task.completed ? 'text-decoration-line-through text-muted' : null"
          >{{task.name}}</span>
          <button
            class="btn btn-primary btn-sm"
            @click="editTask(task)"
          >Editar</button>
          <button
            class="btn btn-danger btn-sm"
            @click="task.state = 'delete'"
          >Excluir</button>
        </template>

        <template v-else-if="task.state === 'edit'">
          <input v-model="task._completed" type="checkbox" class="form-check-input">
          <input v-model="task._name" type="text"  class="form-control form-control-sm">
          <button
            class="btn btn-success btn-sm"
            @click="commitTask(task)"
          >Salvar</button>
          <button
            class="btn btn-secondary btn-sm"
            @click="task.state = 'show'"
          >Cancelar</button>
        </template>

        <template v-else-if="task.state === 'delete'">
          <span class="flex-grow-1">
            <div class="fw-semibold">{{task.name}}</div>
            <div class="text-muted small">Tem certeza que deseja remover?</div>
          </span>
          <button
            class="btn btn-danger btn-sm"
            @click="deleteTask(task)"
          >Sim, excluir</button>
          <button
            class="btn btn-outline-secondary btn-sm"
            @click="task.state = 'show'"
          >Cancelar</button>
        </template>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { computed, ref } from "vue"

const newTask = ref("")
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

function addTask() {
  if (!newTask.value) return

  tasks.value.push({
    id: Date.now(),
    name: newTask.value,
    completed: false,
    state: 'show'
  })
  newTask.value =  ""
}

function editTask(task) {
  if (!Object.hasOwn(task, '_name')) task._name = task.name
  if(!Object.hasOwn(task, '_completed')) task._completed = task.completed

  task.state = 'edit'
}

function commitTask(task) {
  if (!Object.hasOwn(task, '_name')) task.name = task._name
  if (!Object.hasOwn(task, '_completed')) task.completed = task._completed
  task.state = 'show'
  delete task._name
  delete task._completed
}

function deleteTask(task) {
  const index = tasks.value.findIndex(o => o.id === task.id)
  if (index !== -1) {
    tasks.value.splice(index, 1)
  }
}

function clearFilters() {
  filterSearch.value = ''
  filterStatus.value = ''
}

</script>
