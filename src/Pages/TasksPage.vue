<template>
  <main>
    <div class="task-input">
      <input
        type="text"
        placeholder="+ Add new task. Press enter to save."
        id="new-task"
      />
    </div>
    <div class="task-container">
      <h2>My Tasks</h2>
      <ul class="task-list">
        <li>
          <div>
            <input type="checkbox" id="task-1" />
            <label for="task-1">My First task</label>
          </div> 
          <span class="task-date">24 Feb 12:00</span>
          <span class="task-icons">
            <i class="fas fa-edit"></i>
            <i class="fas fa-trash-alt"></i>
          </span>
         
        </li>
      </ul>
    </div>
  </main>
</template>
<script setup>
import { onMounted } from "vue";
import { allTasks } from "../http/task-api";

onMounted(async () => {
  const { data } = await allTasks();
  console.log(data);
});


</script>