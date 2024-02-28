<template>
	<main class="app">
	<section class="create-todo">
		<h3>CREATE A TODO</h3>
		<form id="new-todo-form" @submit.prevent="addTodo">
			<h4>Welcome Back!!!</h4>
		<input type="text" name="content" id="content" placeholder="Enter the task to do" v-model="input_content"/>
		<input type="submit" value="Add todo" />
		</form>
	</section>
  
	<section class="todo-list">
		<h3>TODO LIST</h3>
		<div class="list">
		<ul>
			<li v-for="(todo, index) in sortedTodos" :key="index">
			{{ todo.content }}
			<button @click="removeTodo(todo)">Remove</button>
			</li>
		</ul>
		</div>
	</section>
	</main>
</template>
  
<script>


  
  export default {
	name: 'task_list',
	props: {
	msg: String,
	},
	data() {
	return {
		todos: [],
		input_content: '',
		name: '',
	};
	},
	computed: {
	sortedTodos() {
		return this.todos.slice().sort((a, b) => a.createdAt - b.createdAt);
	},
	},
	watch: {
	name(newVal) {
		localStorage.setItem('name', newVal);
	},
	todos: {
		handler(newVal) {
		localStorage.setItem('todos', JSON.stringify(newVal));
		},
		deep: true,
	},
	},
	methods: {
	addTodo() {
		if (this.input_content.trim() === '') {
		return;
		}
  
		this.todos.push({
		content: this.input_content,
		done: false,
		editable: false,
		createdAt: new Date().getTime(),
		});
  
		this.input_content = ''; // Clear input field after adding a todo
	},
	removeTodo(todo) {
		this.todos = this.todos.filter((t) => t !== todo);
	},
	},
	mounted() {
	this.name = localStorage.getItem('name') || '';
	this.todos = JSON.parse(localStorage.getItem('todos')) || [];
	},
  };
  </script>
  
  <style scoped>
  h3 {
	margin: 40px 0 0;
  }
  ul {
	list-style-type: none;
	padding: 0;
  }
  li {
	display: inline-block;
	margin: 0 10px;
  }
  a {
	color: #42b983;
  }
  </style>
  