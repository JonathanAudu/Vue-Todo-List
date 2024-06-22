<template>
  <div class="task-input">
    <input
      type="text"
      placeholder="+ Add new task. Press enter to save."
      id="new-task"
      @keydown.enter="addNewTask"
    />
  </div>
</template>

<script setup>
import { reactive } from "vue";

const newTask = reactive({
  name: "",
  is_completed: false,
});

const emits = defineEmits(["added"]);

const addNewTask = (event) => {
  if (event.target.value.trim()) {
    newTask.name = event.target.value;
    event.target.value = "";
    emits("added", { ...newTask });
  }
};
</script>