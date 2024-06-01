<script setup lang="ts">
import { computed, ref } from 'vue'
import TheForm from '@/components/TheForm.vue'
import TheTitle from '@/components/TheTitle.vue'
import TheTodo from '@/components/TheTodo.vue'
import TheControls from '@/components/TheControls.vue'
// types imports
import type tasksList from '../types/TasksListType'

// data
let tasksLists = ref<tasksList[] | null>([])
let filter = ref('all')

// function
let pushNewTask = (data: tasksList): void => {
  if (data) {
    tasksLists.value?.unshift(data)
  }
}

// functions to modify tasks list
function checkTask(id: number): void {
  tasksLists.value?.find((e) => (e.id === id ? (e.status = 'completed') : ''))
}

// function to uncheck checkbox
function unCheckTask(id: number): void {
  tasksLists.value?.find((e) => (e.id === id ? (e.status = 'active') : ''))
}

function deleteTask(id: number): void {
  let taskIndex = tasksLists.value?.findIndex((e) => e.id === id)

  if (taskIndex !== undefined && taskIndex >= 0) {
    tasksLists.value?.splice(taskIndex, 1)
  }
}

function filterTasks(e: string) {
  filter.value = e
}

function deleteAllTasks() {
  tasksLists.value?.forEach(() => tasksLists.value?.pop())
}

// computed properties for filtered tasks
let filteredTasks = computed<tasksList[]>(() => {
  if (filter.value === 'all') {
    return tasksLists.value || []
  } else if (filter.value == 'active') {
    return tasksLists.value?.filter((task) => task.status === 'active') || []
  } else if (filter.value === 'completed') {
    return tasksLists.value?.filter((task) => task.status === 'completed') || []
  }
  return []
})

let taskCounts = computed<number>(() => {
  if (tasksLists.value) {
    return tasksLists.value?.length
  } else {
    return 0
  }
})
</script>
<template>
  <main class="main-container">
    <TheTitle />
    <TheForm @@push-data="pushNewTask" />
    <TheTodo
      :tasks="filteredTasks"
      @@delete-task="deleteTask"
      @@check-task="checkTask"
      @@un-check-task="unCheckTask"
    />

    <TheControls @@toggle-tasks="filterTasks" @@clear-tasks="deleteAllTasks" :count="taskCounts" />
  </main>
</template>
<style scoped></style>
