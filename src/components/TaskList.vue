<template>
  <div>
    <h1>Task List</h1>
    <input
      @keyup.enter="addTask"
      v-model="newTask"
      type="text"
      placeholder="Enter some task here"
    />
    <button @click="addTask">Add task</button>
  </div>

  <!-- Add new task -->
  <div>
    <ul>
      <li v-for="(task, index) in taskList" :key="index">
        <input v-model="task.status" type="checkbox" id="checked" />{{ task.name }}
        <button class="deleteButton" @click="deleteTask(index)">🚫</button>
      </li>
    </ul>
  </div>

  <!-- Completed tasks -->
  <div>
    <h2>Completed</h2>
    <div v-for="(task, index) in completedTasks" :key="index">
      {{ task.name }} - {{ task.status }}
    </div>
  </div>

  <!-- Uncompleted tasks -->
  <div>
    <h2>Uncompleted</h2>
    <div v-for="(task, index) in unCompletedTasks" :key="index">
      {{ task.name }} - {{ task.status }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import type { ITask } from '../types/ITask'

const taskList = ref<ITask[]>([])
const newTask = ref<string>('')

const addTask = () => {
  if (newTask.value === '' || taskList.value.find((task) => task.name === newTask.value)) {
    alert('Please enter a available task')
  } else {
    taskList.value.push({
      name: newTask.value,
      status: false
    })
    newTask.value = ''
    console.log(taskList.value)
  }
}

const completedTasks = computed(() => {
  return taskList.value.filter((task) => task.status === true)
})

const unCompletedTasks = computed(() => {
  return taskList.value.filter((task) => !task.status === true)
})

const deleteTask = (index) => {
  taskList.value.splice(index, 1)
}
</script>

<style scoped>
.deleteButton {
  background-color: transparent;
  border: none;
}
.deleteButton:hover {
  box-shadow: 0 1px 1px 1px rgba(73, 72, 72, 0.2), 0 4px 10px 0 rgba(73, 72, 72, 0.19);
  border-radius: 50px;
}
</style>
