<script>
export default {
  data() {
    return {
      todos: [
        {
          id: 1,
          title: '看MyGO',
          completed: true
        },
        {
          id: 2,
          title: '学深度学习',
          completed: false
        },
        {
          id: 3,
          title: '看TED视频',
          completed: false
        }
      ],
      visibility: 'all',
      editedTodo: null
    }
  },
  computed: {
    filteredTodos() {
      if (this.visibility === 'all') {
        return this.todos
      } else if (this.visibility === 'active') {
        return this.todos.filter(todo => !todo.completed)
      } else if (this.visibility === 'completed') {
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    },
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    }
  },
  methods: {
    toggleAll(e) {
      this.todos.forEach(todo => {
        todo.completed = e.target.checked
      })
    },
    onHashChange() {
      const hash = window.location.hash.replace(/#\/?/, '')
      if (['all', 'active', 'completed'].includes(hash)) {
        this.visibility = hash
      } else {
        window.location.hash = ''
        this.visibility = 'all'
      }
    },
    addTodo(e) {
      const title = e.target.value.trim()
      if (!title) {
        return
      }
      this.todos.push({
        id: this.todos.length + 1,
        title,
        completed: false
      })
      e.target.value = ''
    },
    removeTodo(todo) {
      const index = this.todos.indexOf(todo)
      this.todos.splice(index, 1)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    editTodo(todo) {
      this.cacheTitle = todo.title
      this.editedTodo = todo
    },
    doneEdit(todo) {
      if (!this.editedTodo) {
        return
      }
      this.editedTodo = null
      todo.title = todo.title.trim()
      if (!todo.title) {
        this.removeTodo(todo)
      }
    },
    cancelEdit(todo) {
      this.editedTodo = null
      todo.title = this.cacheTitle
    }
  },
  mounted() {
    window.addEventListener('hashchange', this.onHashChange)
    this.onHashChange()
  },
}
</script>

<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input @keyup.enter="addTodo" class="new-todo" placeholder="弄啥嘞？" autofocus>
    </header>
    <!-- This section should be hidden by default and shown when there are todos -->
    <section class="main" v-show="todos.length">
      <input id="toggle-all" class="toggle-all" type="checkbox" @click="toggleAll">
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <!-- These are here just to show the structure of the list items -->
        <!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
        <li v-for="todo in filteredTodos" :key="todo.id" :class="{
          completed: todo.completed, editing: todo == editedTodo
        }">
          <div class="view">
            <input class="toggle" type="checkbox" v-model="todo.completed">
            <label v-text="todo.title" @dblclick="editTodo(todo)"></label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
          <input class="edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)"
            @keyup.esc="cancelEdit(todo)">
        </li>
      </ul>
    </section>
    <!-- This footer should be hidden by default and shown when there are todos -->
    <footer class="footer" v-show="todos.length">
      <!-- This should be `0 items left` by default -->
      <span class="todo-count" v-if="remaining === 0 || remaining === 1"><strong>{{ remaining }}</strong>
        item left</span>
      <span class="todo-count" v-else><strong>{{ remaining }}</strong> items left</span>
      <!-- Remove this if you don't implement routing -->
      <ul class="filters">
        <li>
          <a :class="{ selected: visibility === 'all' }" href="#/all">All</a>
        </li>
        <li>
          <a :class="{ selected: visibility === 'active' }" href="#/active">Active</a>
        </li>
        <li>
          <a :class="{ selected: visibility === 'completed' }" href="#/completed">Completed</a>
        </li>
      </ul>
      <!-- Hidden if no completed items are left ↓ -->
      <button class="clear-completed" @click="clearCompleted" v-show="todos.filter(todo => todo.completed).length">Clear
        completed</button>
    </footer>
  </section>
</template>

<style>
@import "https://unpkg.com/todomvc-app-css@2.4.2/index.css";
</style>
