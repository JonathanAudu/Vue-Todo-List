<template>
    <main>
        <div class="task-input">
            <input type="text" placeholder="+ Add new task. Press enter to save." id="new-task" />
        </div>
        <div class="task-container">
            <h2>My Tasks</h2>
            <ul class="task-list">
                <!-- Uncompleted tasks -->
                <li v-for="task in uncompletedTasks" :key="task.id" :class="{ complete: task.is_completed }">
                    <div>
                        <input type="checkbox" :id="'task-' + task.id" :checked="task.is_completed" />
                        <label :for="'task-' + task.id">{{ task.name }}</label>
                    </div>
                    <span class="task-icons">
                        <i class="fas fa-edit"></i>
                        <i class="fas fa-trash-alt"></i>
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
                <li v-for="task in completedTasks" :key="task.id" :class="{ complete: task.is_completed }">
                    <div>
                        <input type="checkbox" :id="'task-' + task.id" :checked="task.is_completed" />
                        <label :for="'task-' + task.id">{{ task.name }}</label>
                    </div>
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
import { computed, onMounted, ref } from "vue";
import { allTasks } from "../http/task-api";

const tasks = ref([]);

onMounted(async () => {
    const { data } = await allTasks();
    tasks.value = data.data;
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
</script>