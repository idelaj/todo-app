<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todo_list = ref([])
const input_content = ref('')
const todos_order = computed(() => todo_list.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(todo_list, (newVal) => {
	localStorage.setItem('todo_list', JSON.stringify(newVal))
}, {
	deep: true
})

const addItem = () => {
	if (input_content.value.trim() === '') {
		return
	}

	todo_list.value.push({
		content: input_content.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

  input_content.value = ''
}

const deleteItem = (todo) => {
	todo_list.value = todo_list.value.filter((t) => t !== todo)
}

onMounted(() => {
	todo_list.value = JSON.parse(localStorage.getItem('todo_list')) || []
})
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				Hi there!
			</h2>
		</section>
		<section class="create-todo">
			<h3>It is an easy todo-list down below</h3>
			<form id="new-todo-form" @submit.prevent="addItem">
				<h4>What's on your todo list?</h4>
        <div class="input-wrapper">
          <input type="text" name="content" id="content" placeholder="I have to do..." v-model="input_content" />
          <input type="submit" value="Add todo" />
        </div>
			</form>
		</section>

		<section class="todo-list">
			<h3>Your todo list</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_order" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
            <span class="bubble personal"></span>

					</label>
					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="deleteItem(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>