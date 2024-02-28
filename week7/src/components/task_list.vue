<template>
	<main class="app">
		<section class="create-todo">
			<h3>CREATE A TODO</h3>
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Welcome Back!!!</h4>
				<input type="text" name="content" id="content" placeholder="Enter the task to do" v-model="input_content" :required="true">
				<input type="datetime-local" name="deadline" id="deadline" v-model="input_deadline" class="date-input" :required="true">
				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list">
				<ul>
					<li v-for="(todo, index) in sortedTodos" :key="index" :class="{ overdue: isOverdue(todo), upcoming: isUpcoming(todo) }">
						<input type="checkbox" v-model="todo.done" class="checkbox"/>
						<span :class="{ completed: todo.done }">{{ todo.content }}</span>
						<span class="due-date">Due: {{ formatDueDate(todo.deadline) }}</span>
						<span v-if="isOverdue(todo)" class="alert">Overdue</span>
						<span v-if="isUpcoming(todo)" class="alert">Upcoming</span>
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
			input_deadline: '',
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
			deadline: new Date(this.input_deadline).getTime(),
		});

		this.input_content = ''; // Clear input field after adding a todo
		this.input_deadline = ''; // Clear input field after adding a todo
		},
		removeTodo(todo) {
			this.todos = this.todos.filter((t) => t !== todo);
		},
		isOverdue(todo) {
			return !todo.done && todo.deadline < new Date().getTime();
		},
		isUpcoming(todo) {
			const today = new Date().getTime();
			const oneDay = 24 * 60 * 60 * 1000; // One day in milliseconds
			return !todo.done && todo.deadline > today && todo.deadline <= today + oneDay;
		},
		formatDueDate(timestamp) {
			const date = new Date(timestamp);
			return `${date.toLocaleDateString()} ${date.toLocaleTimeString()}`;
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

	.overdue {
		background-color: #FF0000; /* Red for overdue tasks */
		color: #FFFFFF;
	}

	.upcoming {
		background-color: #FFA500; /* Orange for upcoming tasks */
		color: #000000;
	}

	.alert {
	margin-left: 10px;
	font-weight: bold;
	}

	.completed {
		text-decoration: line-through;
	}

	.checkbox {
		margin-right: 5px;
	}

	.date-input {
		width: 200px;
	}

	.due-date {
		margin-left: 10px;
	}

</style>
