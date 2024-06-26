<template>
  <main>
    <NewTask @added="handleAddedTask" />

    <div class="task-container">
      <h2>My Tasks</h2>
      <ul class="task-list">
        <!-- Uncompleted tasks -->
        <li
          v-for="task in uncompletedTasks"
          :key="task.id"
          :class="{ complete: task.is_completed }"
        >
          <div class="align-task">
            <input
              type="checkbox"
              :id="'task-' + task.id"
              :checked="task.is_completed"
              @change="toggleTaskCompletion(task)"
            />
            <div v-if="!task.isEditing">
              <label :for="'task-' + task.id">{{ task.name }}</label>
            </div>
            <div v-else>
              <input
                type="text"
                v-model="task.name"
                @keydown.enter="saveTask(task)"
                class="borderless-input"
              />
            </div>
          </div>
          <span class="task-icons" v-if="!task.isEditing">
            <button @click="editTask(task)" v-if="!task.is_completed">
              <i class="fas fa-edit"></i>
            </button>
            <button @click="confirmRemoveTask(task)">
              <i class="fas fa-trash-alt"></i>
            </button>
          </span>
        </li>
      </ul>

      <div id="comButton" v-show="showToggleCompleteBtn">
        <button @click="showCompletedTask = !showCompletedTask">
          <span v-if="!showCompletedTask">Show Completed Tasks</span>
          <span v-else>Hide Completed Tasks</span>
        </button>
        <hr />
      </div>

      <ul class="task-list" v-if="showCompletedTask">
        <!-- Completed tasks -->
        <li
          v-for="task in completedTasks"
          :key="task.id"
          :class="{ complete: task.is_completed }"
        >
          <div class="align-task">
            <input
              type="checkbox"
              :id="'task-' + task.id"
              :checked="task.is_completed"
              @change="toggleTaskCompletion(task)"
            />
            <div v-if="!task.isEditing">
              <label :for="'task-' + task.id">{{ task.name }}</label>
            </div>
            <div v-else>
              <input
                type="text"
                v-model="task.name"
                @keydown.enter="saveTask(task)"
                class="borderless-input"
              />
            </div>
          </div>
          <span class="task-icons" v-if="!task.isEditing">
            <button @click="confirmRemoveTask(task)">
              <i class="fas fa-trash-alt"></i>
            </button>
          </span>
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";
import {
  allTasks,
  createTask,
  updateTask,
  deleteTask,
  completeTask,
} from "../http/task-api";
import NewTask from "../components/tasks/NewTask.vue";

const tasks = ref([]);

onMounted(async () => {
  const { data } = await allTasks();
  tasks.value = data.data.map((task) => ({ ...task, isEditing: false }));
});

const completedTasks = computed(() =>
  tasks.value.filter((task) => task.is_completed)
);
const uncompletedTasks = computed(() =>
  tasks.value.filter((task) => !task.is_completed)
);

const showCompletedTask = ref(false);

const showToggleCompleteBtn = computed(
  () => uncompletedTasks.value.length > 0 && completedTasks.value.length > 0
);

const handleAddedTask = async (newTask) => {
  const { data: createdTask } = await createTask(newTask);
  tasks.value.unshift({ ...createdTask.data, isEditing: false });
};

const editTask = (task) => {
  task.isEditing = true;
};

const saveTask = async (task) => {
  if (task.name.trim() === "") {
    alert("Task name cannot be empty.");
    return;
  }
  await updateTask(task.id, {
    name: task.name,
    is_completed: task.is_completed,
  });
  task.isEditing = false;
};

const confirmRemoveTask = async (task) => {
  if (confirm("Are you sure you want to delete this task?")) {
    await deleteTask(task.id);
    tasks.value = tasks.value.filter((t) => t.id !== task.id);
  }
};

const toggleTaskCompletion = async (task) => {
  const updatedTask = { ...task, is_completed: !task.is_completed };

  try {
    const response = await completeTask(task.id, {
      is_completed: updatedTask.is_completed,
    });
    if (response.data) {
      task.is_completed = updatedTask.is_completed;
    }
  } catch (error) {
    console.error("Failed to update task completion status", error);
  }
};
</script>
