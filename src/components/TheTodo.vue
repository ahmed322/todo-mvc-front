<script setup lang="ts">
import { watch, ref } from "vue";
import type TasksList from "../types/TasksListType";
import { OhVueIcon, addIcons } from "oh-vue-icons";
import {
	RiCheckboxBlankCircleLine,
	RiCheckboxCircleLine,
} from "oh-vue-icons/icons";

// props
let props = defineProps<{ tasks: TasksList[] | null }>();
const localTasks = ref<TasksList[] | null>(props.tasks);

// emits
const emit = defineEmits<{
	(e: "@checkTask", payload: string): void;
	(e: "@unCheckTask", payload: string): void;
	(e: "@deleteTask", payload: string): void;
}>();
//
watch(
	() => props.tasks,
	(newTasks) => {
		localTasks.value = newTasks;
	}
);
// function to check checkbox

function checkTask(_id: string): void {
	emit("@checkTask", _id);
	// props.tasks?.find((e) => (e.id === id ? (e.status = 'completed') : ''))
}
// function to uncheck checkbox
function unCheckTask(_id: string): void {
	emit("@unCheckTask", _id);
	props.tasks?.find((e) => (e._id === _id ? (e.status = "active") : ""));
}

function deleteTask(_id: string): void {
	emit("@deleteTask", _id);
}

// regiesiter vue icons
addIcons(RiCheckboxBlankCircleLine, RiCheckboxCircleLine);
</script>

<template>
	<article class="task-container" v-if="props.tasks">
		<div v-for="task in props.tasks" :key="task._id">
			<!-- task status icon -->
			<span type="checkbox" class="toggle-task">
				<OhVueIcon
					v-if="task.status === 'active'"
					@click="checkTask(task._id)"
					name="ri-checkbox-blank-circle-line"
					scale="1.5"
					fill="#aaa"
				/>
				<OhVueIcon
					v-if="task.status === 'completed'"
					@click="unCheckTask(task._id)"
					name="ri-checkbox-circle-line"
					scale="1.5"
					fill="#59B4A4"
				/>
			</span>
			<p :class="{ completed: task.status === 'completed' }">{{ task.task }}</p>
			<span class="cancel" @click="deleteTask(task._id)"><span>x</span></span>
		</div>
	</article>
</template>

<style scoped>
.task-container {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
}

.task-container div {
	display: flex;
	font-weight: 100;
	align-items: center;
	/* color: rgba(0, 0, 0, 0.565); */
	border: 1px solid #eee;
	outline: none;
	padding: 20px 15px;
	font-size: 20px;
	width: 490px;
	border-radius: 2px;
	-webkit-font-smoothing: antialiased;
}

.task-container div .toggle-task {
	width: 10%;
	margin-right: 10px;
	border-radius: 50%;
	background: none;
}

.task-container div p {
	width: 80%;
}

.task-container div .completed {
	position: relative;
	color: rgba(0, 0, 0, 0.565);
	text-decoration: line-through;
	text-decoration-color: rgba(0, 0, 0, 0.565);
}

.task-container div .cancel {
	width: 10%;
	text-align: right;
}

.task-container div .cancel span {
	display: none;
	opacity: 0%;
	transition-duration: 0.3s;
	cursor: pointer;
}
.task-container:hover div .cancel span {
	display: inline-block;
	opacity: 50%;
}
</style>
