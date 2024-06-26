<script setup lang="ts">
import { computed, ref, onMounted } from "vue";
import axios from "axios";
import TheForm from "@/components/TheForm.vue";
import TheTitle from "@/components/TheTitle.vue";
import TheTodo from "@/components/TheTodo.vue";
import TheControls from "@/components/TheControls.vue";
// types imports
import type tasksList from "../types/TasksListType";

// data
let tasksLists = ref<tasksList[] | null>([]);
let filter = ref("all");
const baseUrl = import.meta.env.VITE_API_URL;

// send new task to server
let pushNewTask = (data: tasksList): void => {
	// axios config object for post request
	let config = {
		method: "post",
		url: baseUrl,
		headers: {
			"Content-Type": "application/json",
		},
		data: data,
	};

	// send data to server
	axios
		.request(config)
		.then((res) => {
			console.log(res.data);
			// push data to array if it success
			if (res.status) {
				tasksLists.value?.unshift(res.data);
				// console.log(res.data.message) // Optional: Log success message
			}
		})
		.catch((err) => console.log(err));
};

let getTasks = (): void => {
	// axios config object for get request
	let config = {
		method: "get",
		url: baseUrl,
		headers: {
			"Content-Type": "application/json",
		},
	};

	// send data to server
	axios
		.request(config)
		.then((res) => {
			// push data to array if it success
			if (res.status) {
				tasksLists.value = res.data;
				// console.log(res.data.message) // Optional: Log success message
			}
		})
		.catch((err) => console.log(err));
};

// functions to modify tasks list
function checkTask(id: string): void {
	// axios config object for post request
	let config = {
		method: "put",
		url: baseUrl + "/" + id,
		headers: {
			"Content-Type": "application/json",
		},
		data: { status: "completed" },
	};

	// send data to server
	axios
		.request(config)
		.then((res) => {
			// push data to array if it success
			if (res.status) {
				tasksLists.value?.find((e) =>
					e._id === id ? (e.status = "completed") : ""
				);
				// console.log(res.data.message) // Optional: Log success message
			}
		})
		.catch((err) => console.log(err));
}

// function to uncheck checkbox
function unCheckTask(id: string): void {
	// axios config object for post request
	let config = {
		method: "put",
		url: baseUrl + "/" + id,
		headers: {
			"Content-Type": "application/json",
		},
		data: { status: "active" },
	};

	// send data to server
	axios
		.request(config)
		.then((res) => {
			// push data to array if it success
			if (res.status) {
				tasksLists.value?.find((e) =>
					e._id === id ? (e.status = "active") : ""
				);
				// console.log(res.data.message) // Optional: Log success message
			}
		})
		.catch((err) => console.log(err));
}
// ! fix delete not work well
function deleteTask(id: string): void {
	let config = {
		method: "delete",
		url: baseUrl + "/" + id,
		headers: {
			"Content-Type": "application/json",
		},
	};

	// send data to server
	axios
		.request(config)
		.then((res) => {
			// push data to array if it success
			if (res.status) {
				let taskIndex = tasksLists.value?.findIndex((e) => e._id === id);

				if (taskIndex !== undefined && taskIndex >= 0) {
					tasksLists.value?.splice(taskIndex, 1);
				}
			}
		})
		.catch((err) => console.log(err));
}

function filterTasks(e: string) {
	filter.value = e;
}

function deleteCompletedTasks() {
	let config = {
		method: "delete",
		url: baseUrl,
		headers: {
			"Content-Type": "application/json",
		},
	};

	axios
		.request(config)
		.then((res) => {
			if (res.status && res.data) {
				tasksLists.value = res.data;
			}
		})
		.catch((err) => console.log(err));
}

// computed properties for filtered tasks
let filteredTasks = computed<tasksList[]>(() => {
	if (filter.value === "all") {
		return tasksLists.value || [];
	} else if (filter.value == "active") {
		return tasksLists.value?.filter((task) => task.status === "active") || [];
	} else if (filter.value === "completed") {
		return (
			tasksLists.value?.filter((task) => task.status === "completed") || []
		);
	}
	return [];
});

let taskCounts = computed<number>(() => {
	if (tasksLists.value) {
		return tasksLists.value?.length;
	} else {
		return 0;
	}
});

// hooks
onMounted(() => {
	// get tasks wehn mount the app
	getTasks();
});
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

		<TheControls
			@@toggle-tasks="filterTasks"
			@@clear-tasks="deleteCompletedTasks"
			:count="taskCounts"
		/>
	</main>
</template>
<style scoped></style>
