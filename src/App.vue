<template>
  <div class="todo-app">
    <h1>Todo App</h1>

    <div class="input-group">
      <input type="text" placeholder="Enter new task" v-model="newTodo" @keyup.enter="addTodo" />
      <button @click="addTodo">Add</button>
    </div>

    <div class="tabs">
      <button :class="{ active: activeTab === 'all' }" @click="activeTab = 'all'">All</button>
      <button :class="{ active: activeTab === 'active' }" @click="activeTab = 'active'">Active</button>
      <button :class="{ active: activeTab === 'completed' }" @click="activeTab = 'completed'">Completed</button>
    </div>

    <div class="todos">
      <div v-if="activeTab === 'all'">
        <h2>All Todos</h2>
        <ul>
          <li v-for="todo in todos" :key="todo.id">
            <div>
              <input type="checkbox" :id="todo.id" v-model="todo.completed" />
              <label :for="todo.id" :class="{ completed: todo.completed }">{{ todo.title }}</label>
              <span class="created-date">({{ formatDate(todo.createdAt) }})</span>
            </div>
            <div class="actions">
              <button class="edit" @click="editTodo(todo)">Edit</button>
              <button class="delete" @click="deleteTodo(todo)">Delete</button>
            </div>
            <div v-if="todo.editing" class="edit-todo">
              <input type="text" v-model="todo.title" @keyup.enter="saveTodo" />
              <button @click="saveTodo">Save</button>
            </div>
          </li>
        </ul>
      </div>

      <div v-if="activeTab === 'active'">
        <h2>Active Todos</h2>
        <ul>
          <li v-for="todo in activeTodos" :key="todo.id">
            <div>
              <input type="checkbox" :id="todo.id" v-model="todo.completed" />
              <label :for="todo.id">{{ todo.title }}</label>
              <span class="created-date">({{ formatDate(todo.createdAt) }})</span>
            </div>
            <div class="actions">
              <button class="edit" @click="editTodo(todo)">Edit</button>
              <button class="delete" @click="deleteTodo(todo)">Delete</button>
            </div>
            <div v-if="todo.editing" class="edit-todo">
              <input type="text" v-model="todo.title" @keyup.enter="saveTodo" />
              <button @click="saveTodo">Save</button>
            </div>
          </li>
        </ul>
      </div>

      <div v-if="activeTab === 'completed'">
        <h2>Completed Todos</h2>
        <ul>
          <li v-for="todo in completedTodos" :key="todo.id">
            <div>
              <input type="checkbox" :id="todo.id" v-model="todo.completed" />
              <label :for="todo.id" :class="{ completed: todo.completed }">{{ todo.title }}</label>
              <span class="created-date">({{ formatDate(todo.createdAt) }})</span>
            </div>
            <div class="actions">
              <button class="edit" @click="editTodo(todo)">Edit</button>
              <button class="delete" @click="deleteTodo(todo)">Delete</button>
            </div>
            <div v-if="todo.editing" class="edit-todo">
              <input type="text" v-model="todo.title" @keyup.enter="saveTodo" />
              <button @click="saveTodo">Save</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'TodoApp',

  data() {
    return {
      todos: [],
      newTodo: '',
      activeTab: 'all',
    }
  },

  computed: {
    activeTodos() {
      return this.todos.filter((todo) => !todo.completed)
    },
    completedTodos() {
      return this.todos.filter((todo) => todo.completed)
    },
  },

  methods: {
    addTodo() {
      const title = this.newTodo.trim()
      if (title) {
        const todo = {
          id: Date.now(),
          title,
          completed: false,
          createdAt: new Date(),
          editing: false,
        }
        this.todos.push(todo)
        this.newTodo = ''
        this.saveTodos()
      }
    },

    editTodo(todo) {
      todo.editing = true
    },

    saveTodo() {
      this.todos.forEach((todo) => {
        if (todo.editing) {
          todo.title = todo.title.trim()
          todo.editing = false
        }
      })
      this.saveTodos()
    },

    deleteTodo(todo) {
      const index = this.todos.indexOf(todo)
      if (index !== -1) {
        this.todos.splice(index, 1)
        this.saveTodos()
      }
    },

    deleteTodoForever(todo) {
      const index = this.todos.indexOf(todo)
      if (index !== -1) {
        this.todos.splice(index, 1)
      }
    },

    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' }
      return new Date(date).toLocaleDateString(undefined, options)
    },

    saveTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },

    loadTodos() {
      const todos = localStorage.getItem('todos')
      if (todos) {
        this.todos = JSON.parse(todos)
      }
    },
  },

  created() {
    this.loadTodos()
  },
}
</script>
<style>
body {
  background-color: #92b79f6e;
  font-family: Arial, sans-serif;
}
h1 {
  text-align: center;
  color: #333;
}
.input-group {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 1.25rem 0;
}
.input-group input[type="text"] {
  font-size: 1rem;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  width: 300px;
}
.input-group button {
  font-size: 1rem;
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: #2ecc71;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.input-group button:hover {
  background-color: #27ae60;
}
.tabs {
  display: flex;
  justify-content: center;
  margin-bottom: 1.25rem;
}
.tabs button {
  font-size: 1rem;
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: #ad60cb;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}
.tabs button:hover {
  background-color: #7915a4;
}
.tabs button.active {
  background-color: #72797e;
}
.tabs button.active:hover {
  background-color: #4d555a;
}
.todos {
  margin-bottom: 1rem;
}
.todos h2 {
  font-size: 1.5rem;
  color: #333;
  margin-top: 5rem;
  text-align: center;
}
.todos ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
.todos li {
  margin: 10px 0;
  padding: 10px;
  background-color: #407bf838;
  border: 1px solid #ccc;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.todos li label {
  margin-left: 10px;
}
.todos li label.completed {
  text-decoration: line-through;
  color: #999;
}
.todos li .actions button {
  font-size: 1rem;
  padding: 5px;
  border: none;
  border-radius: 5px;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}
.todos li .actions .edit {
  background-color: #727267;
}
.todos li .actions .delete{
  background-color: #e74c3c;
}
.todos li .actions .edit:hover {
  background-color: #56564b;
}
.todos li .actions .delete:hover {
  background-color: #c0392b;
}
.todos li .edit-todo input[type="text"] {
  font-size: 1rem;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  width: 300px;
}
.todos li .edit-todo button {
  font-size: 1rem;
  padding: 5px;
  border: none;
  border-radius: 5px;
  background-color: #2ecc71;
  color: #fff;
  cursor: pointer;
}
.todos li .edit-todo button:hover {
  background-color: #27ae60;
}
.created-date {
  margin-left: 1rem;
  font-size: .75rem;
}
</style>
