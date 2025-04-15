<template>
  <div class="container d-flex justify-content-center align-items-center">
    <div class="card mt-3 col-9 col-md-7 main-container">
      <div class="card-body">
        <h1 class="text-center">To-Do List</h1>
        <div class="d-flex flex-row justify-content-around my-3">
          <div class="col-9">
            <input
              type="text"
              class="form-control"
              placeholder="Add a new task..."
              v-model="newTask"
              @keyup.enter="addTask()"
              :disabled="tasks.length > 4"
            />
          </div>
          <div class="mt-sm-0">
            <button
              type="button"
              class="btn btn-primary"
              @click="addTask()"
              v-if="tasks.length <= 4"
            >
              Add
            </button>
            <p v-else class="message">List completed</p>
          </div>
        </div>
        <div
          class="card item-card mt-2"
          v-for="(task, index) in tasks"
          :key="index"
        >
          <div class="d-flex align-items-center justify-content-between p-2">
            <div>
              <input
                type="checkbox"
                v-model="task.isDone"
                class="mr-2 text-wrap"
              />{{ task.description }}
            </div>
            <button
              type="button"
              class="btn btn-danger btn-sm ml-4"
              @click="deleteTask()"
            >
              X
            </button>
          </div>
        </div>
        <div class="mt-3">
          <p class="fw-bold" v-show="pendingTasks > 0">
            You have {{ pendingTasks }} pending
            {{ pendingTasks === 1 ? "task" : "tasks" }}
          </p>
        </div>
        <div class="d-flex justify-content-center">
          <button
            type="button"
            class="btn btn-secondary ml-4"
            @click="deleteAllTasks()"
            v-show="tasks.length != 0"
          >
            Clear all tasks
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed, watch } from "vue";

export default {
  name: "HomePage",
  setup() {
    const newTask = ref("");
    const tasks = ref([]);

    const addTask = () => {
      if (!newTask.value) return;
      tasks.value.unshift({
        description: newTask.value,
        isDone: false,
      });
      localStorage.setItem("tasks", JSON.stringify(tasks.value));
      newTask.value = "";
    };

    const deleteTask = (index) => {
      tasks.value.splice(index, 1);
      localStorage.setItem("tasks", JSON.stringify(tasks.value));
    };

    const deleteAllTasks = () => {
      tasks.value = [];
      localStorage.removeItem("tasks");
    };

    const pendingTasks = computed(() => {
      return tasks.value.filter((x) => x.isDone === false).length;
    });

    watch(
      tasks,
      () => {
        localStorage.setItem("tasks", JSON.stringify(tasks.value));
      },
      { deep: true }
    );

    onMounted(() => {
      if (localStorage.tasks) {
        tasks.value = JSON.parse(localStorage.getItem("tasks")) || [];
      }
    });

    return {
      newTask,
      tasks,
      addTask,
      deleteTask,
      deleteAllTasks,
      pendingTasks,
    };
  },
};
</script>

<style scoped>
.main-container {
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}
.btn-primary {
  background-color: #31024a;
  border: transparent;
}

.message {
  color: green;
  font-weight: bold;
}

.item-card {
  min-height: 60px;
  border: solid 1px #8b898c;
  line-height: 1.25rem;
  word-break: break-word;
}
</style>
