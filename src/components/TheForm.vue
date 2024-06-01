<script setup lang="ts">
import { onMounted, ref } from 'vue'
import type tasksList from '@/types/TasksListType'
import { OhVueIcon, addIcons } from 'oh-vue-icons'
import { MdKeyboardarrowdownTwotone } from 'oh-vue-icons/icons'

//data
let input = ref<HTMLTemplateElement | null>(null)
let task = ref('')
let MaxCharacterCount = 40

// vue events
const emit = defineEmits<{
  (e: '@pushData', payload: tasksList): void
}>()

// function
let pushData = () => {
  if (task.value.trim().length) {
    let newTask: tasksList = { task: task.value.trim(), id: Date.now(), status: 'active' }
    emit('@pushData', newTask)
    task.value = ''
  }
}

let characterCountCheck = (e: Event) => {
  const input = e.target as HTMLInputElement
  if (input.value.length > MaxCharacterCount) {
    input.value = input.value.substring(0, MaxCharacterCount)
  }
  task.value = input.value
}

onMounted(() => {
  input.value?.focus()
})

// regester icons
addIcons(MdKeyboardarrowdownTwotone)
</script>
<template>
  <div class="container">
    <form @submit.prevent="pushData">
      <div class="input-parent">
        <span class="arrow-icon"
          ><OhVueIcon name="md-keyboardarrowdown-twotone" scale="1.5"
        /></span>
        <input
          ref="input"
          type="text"
          placeholder="What needs to be done?"
          :value="task"
          @keyup="characterCountCheck"
        />
      </div>
    </form>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.input-parent {
  border: 2px solid #eee;
  margin-top: 10px;
  /* box-shadow: inset 0 -2px 1px #00000008; */
  box-shadow: 0px 0px 3px 1px transparent;
}

form input {
  font-weight: 100;
  color: rgba(0, 0, 0, 0.565);
  border: none;
  outline: none;
  padding: 20px 15px;
  font-size: 20px;
  width: 449px;
  border-radius: 2px;
  -webkit-font-smoothing: antialiased;
}

.input-parent:focus-within {
  box-shadow: 0px 0px 3px 1px var(--main);
  /* border: none; */
}

/* style arrow icon  */
.arrow-icon {
  border: none;
  outline: none;
  padding: 25px 5px;
  cursor: pointer;
}

.arrow-icon svg {
  fill: #aaa;
}
</style>
