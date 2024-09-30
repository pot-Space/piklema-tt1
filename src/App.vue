<template>
	<div class="todo">
		<h1 class="todo__headline">TODO</h1>

		<input
			class="todo__task-input"
			v-model="newTask"
			@keyup.enter="addTask"
			type="text"
			placeholder="Новая задача"
		/>

		<ul class="todo__list">
			<li
				class="todo__list-item"
				v-for="task in tasks"
				:key="task.id"
				:class="{ completed: task.completed }"
			>
				<input
					class="todo__btn-complete"
					type="checkbox"
					v-model="task.completed"
					@change="updateLocalStorage"
				/>

				<span class="todo__text" @dblclick="editTask(task)">{{
					task.text
				}}</span>

				<textarea
					class="todo__text-input"
					v-if="editingTask && editingTask.id === task.id"
					v-model="editingTask.text"
					@keyup.enter="saveTask"
					@blur="saveTask"
				/>

				<button class="todo__btn-delete" @click="deleteTask(task.id)">✘</button>
			</li>
		</ul>
	</div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const tasks = ref([])
const newTask = ref('')
const editingTask = ref(null)

onMounted(() => {
	const storedTasks = localStorage.getItem('tasks')
	if (storedTasks) {
		tasks.value = JSON.parse(storedTasks)
	}
})

watch(
	tasks,
	newTasks => {
		localStorage.setItem('tasks', JSON.stringify(newTasks))
	},
	{ deep: true }
)

const addTask = () => {
	if (newTask.value.trim()) {
		tasks.value.push({
			id: Date.now(),
			text: newTask.value,
			completed: false,
		})
		newTask.value = ''
	}
}

const deleteTask = id => {
	tasks.value = tasks.value.filter(task => task.id !== id)
}

const editTask = task => {
	editingTask.value = { ...task }
}

const saveTask = () => {
	if (editingTask.value) {
		const taskIndex = tasks.value.findIndex(
			task => task.id === editingTask.value.id
		)

		if (taskIndex !== -1 && editingTask.value.text.trim().length > 0) {
			tasks.value[taskIndex].text = editingTask.value.text
		}
		editingTask.value = null
	}
}

const updateLocalStorage = () => {
	localStorage.setItem('tasks', JSON.stringify(tasks.value))
}
</script>
