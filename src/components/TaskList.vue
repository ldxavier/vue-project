<template>
  <!-- Input and Button -->
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

  <!-- Filter tasks -->
  <div>
    <select name="order" @change="handleOrderChange">
      <option value="" disabled selected>Select a filter</option>
      <option value="ordered">Order by name</option>
      <option value="desordered">Desorder by name</option>
      <option value="added">Order by addition to list</option>
      <option value="completed">Completed tasks</option>
      <option value="uncompleted">Uncompleted tasks</option>
    </select>
  </div>

  <!-- Clear all tasks -->
  <div>
    <button @click="clearAll">Clear</button>
  </div>

  <!-- Add new task -->
  <div>
    <ul>
      <li v-for="(task, index) in taskList" :key="index" :class="{ statusColor: task.status }">
        <input v-model="task.status" type="checkbox" id="checked" />{{ task.name }}
        <button class="deleteButton" @click="deleteTask(index)">🚫</button>
      </li>
    </ul>
  </div>

  <!-- Completed tasks -->
  <div>
    <h2>Completed</h2>
    <div v-for="(task, index) in completedTasks" :key="index">
      {{ task.name }} → {{ task.status }}
    </div>
  </div>

  <!-- Uncompleted tasks -->
  <div>
    <h2>Uncompleted</h2>
    <div v-for="(task, index) in unCompletedTasks" :key="index">
      {{ task.name }} → {{ task.status }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import type { ITask } from '../types/ITask'

const taskList = ref<ITask[]>([])
const newTask = ref<string>('')

const addTask = () => {
  if (newTask.value === '' || taskList.value.find((task) => task.name === newTask.value)) {
    alert('Please enter a available task')
  } else {
    // add task to list
    taskList.value.push({
      name: newTask.value,
      status: false,
      date: Date.now()
    })
    //add task to local storage
    localStorage.setItem('taskList', JSON.stringify(taskList.value))
    newTask.value = ''
    console.log(taskList.value)
  }
}

// Save tasks in local storage
onMounted(() => {
  taskList.value = JSON.parse(localStorage.getItem('taskList') || '[]')
})

const completedTasks = computed(() => {
  return taskList.value.filter((task) => task.status === true)
})

const unCompletedTasks = computed(() => {
  return taskList.value.filter((task) => !task.status === true)
})

// delete task from list
const deleteTask = (index: any) => {
  // remove task from list
  taskList.value.splice(index, 1)
  // update local storage
  localStorage.setItem('taskList', JSON.stringify(taskList.value))
}

// clear all tasks
const clearAll = () => {
  if (confirm('Are you sure you want to clear all tasks?')) {
    localStorage.removeItem('taskList')
    taskList.value = JSON.parse(localStorage.getItem('taskList') || '[]')
  }
  else {
    return
  }
}

const sortName = () => {
  taskList.value.sort((a, b) => a.name.localeCompare(b.name))
}

const desorderName = () => {
  taskList.value.sort((a, b) => b.name.localeCompare(a.name))
}

const sortAdded = () => {
  // sort by added date
  taskList.value.sort((a, b) => a.date - b.date)
}

const filterCompleted = () => {
  taskList.value.sort((a, b) => {
    return a.status === b.status ? 0 : a.status ? -1 : 1
  })
}

const filterUncompleted = () => {
  taskList.value.sort((a, b) => {
    return a.status === b.status ? 0 : b.status ? -1 : 1
  })
}

const handleOrderChange = (event: any) => {
  const selectedValue = event.target.value
  switch (selectedValue) {
    case 'ordered':
      sortName()
      break
    case 'desordered':
      desorderName()
      break
    case 'added':
      sortAdded()
      break
    case 'completed':
      filterCompleted()
      break
    case 'uncompleted':
      filterUncompleted()
      break
    default:
  }
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

.statusColor {
  background-color: rgb(136, 182, 91);
  width: 40%;
  border-radius: 5px;
  padding: 2px;
}
</style>
