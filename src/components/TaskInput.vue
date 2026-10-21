<template>
  <div class="input-group mb-3">
    <input
      type="text"
      placeholder="Adicionar uma nova tarefa..."
      class="form-control"
      v-model="task"
      @keyup.enter="addTask"
    />
    <button class="btn btn-success" @click="addTask">Adicionar</button>
  </div>
</template>

<script setup>
import { ref, defineEmits } from "vue";

const emit = defineEmits(["add-task"]);
const task = ref("");

function addTask() {
  if (!task.value) return;
  emit("add-task", task.value);
  task.value = "";
}
</script>
