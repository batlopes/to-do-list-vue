<template>
  <div class="d-flex gap-2 mb-3">
    <input
      type="text"
      placeholder="Buscar tarefa..."
      class="form-control"
      style="flex: 1"
      v-model="search"
    />
    <select class="form-select" style="flex: 1" v-model="status">
      <option value="">Todas</option>
      <option value="pending">Pendentes</option>
      <option value="completed">Concluídas</option>
    </select>
    <button
      class="btn btn-outline-secondary btn-sm"
      style="flex-shrink: 0"
      @click="clearFilters"
    >
      Limpar filtros
    </button>
  </div>
</template>

<script setup>
const search = defineModel('search')
const status = defineModel('status')
function clearFilters() {
  search.value = ''
  status.value = ''
}
</script>
