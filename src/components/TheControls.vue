<script setup lang="ts">
import { ref } from 'vue'

// data
let active = ref('all')

// props
defineProps<{
  count: number
}>()
// function
let toggleButtons = (e: string): void => {
  active.value = e
  // emit to filter tasks
  emit('@toggleTasks', active.value)
}

function clearTasks() {
  emit('@clearTasks')
}

// vue events

let emit = defineEmits<{
  (event: '@toggleTasks', payload: string): void
  (event: '@clearTasks'): void
}>()
</script>

<template>
  <footer class="footer-parent">
    <div class="controls">
      <p class="item-count">{{ count }} items left</p>
      <ul class="transition-links">
        <li :class="{ active: active === 'all' }" @click="toggleButtons('all')">All</li>
        <li :class="{ active: active === 'active' }" @click="toggleButtons('active')">active</li>
        <li :class="{ active: active === 'completed' }" @click="toggleButtons('completed')">
          Completed
        </li>
      </ul>
      <span class="clear-completed" @click="clearTasks()">Clear Completed</span>
    </div>
  </footer>
</template>
<style scoped>
footer {
  display: flex;
  justify-content: center;
}

div {
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
  width: 490px;
  border: 1px solid #eee;
  position: relative;
}

div::before,
div::after {
  content: ' ';
  position: absolute;
  top: 10px;
  z-index: -1;
  left: 0;
  width: 100%;
  height: 100%;
  border: 1px solid #eee;
  border-color: transparent #eee #eee #eee;
}

div::before {
  top: 4px;
}
div::after {
  top: 8px;
}

div .item-count {
  display: flex;
  align-items: center;
}

.transition-links {
  display: flex;
  /* transform: translateX(-50%); */
}

.transition-links li {
  margin-right: 10px;
  padding: 5px;
  border: 1px solid transparent;
  transition: border 0.3s;
  border-radius: 5px;
  cursor: pointer;
}

.transition-links .active {
  border: 1px solid var(--main);
}

.clear-completed {
  cursor: pointer;
  display: flex;
  align-items: center;
  transition-duration: 0.3s;
}

.clear-completed:hover {
  text-decoration: underline;
  text-decoration-color: #aaa;
}
</style>
