<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_date=ref('')
const input_category = ref(null)
const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))
watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})
watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})
const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null ) {
		return
	}
	todos.value.push({
		content: input_content.value,
		category: input_category.value,
    date:input_date.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}
onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		<div class="left">
		<section class="greeting">
			<h2 class="title">
			Dobrodošli na aplikaciju za planiranje, 
			</h2>
			<input type="text" id="name" placeholder="Unesite ime" v-model="name">
		</section>

		<section class="create-todo">
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Šta imaš u planu da uradiš? *</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="npr. Sredi stan"
					v-model="input_content" />
					<h4>Odaberite kategoriju *</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Posao</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Privatno</div>
					</label>
          <label>
						<input 
							type="radio" 
							name="category" 
							id="category3" 
							value="school"
							v-model="input_category" />
						<span class="bubble school"></span>
						<div>Škola</div>
					</label>

				</div>
				
				
          <h4>Kada to moras da uradiš?</h4>
				<input 
					type="date" 
					name="date" 
					id="date" 
					v-model="input_date" />
					<input type="submit" value="Dodaj"/>
			</form>
		</section>
	</div>
		<section class="todo-list">
			<h2>Lista zadataka:</h2>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business'
								? 'business' 
								: todo.category == 'personal' ? 'personal' : 'school'
						}`"></span>
					</label>

					<div class="todo-content">
			<h4  >{{todo.content}}</h4>
            <h4  >{{todo.date}}</h4>
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Obriši</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>