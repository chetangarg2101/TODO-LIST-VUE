<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const input_content = ref('')

// No need to sort if you're adding to the top
const todos_asc = computed(() => todos.value)

const addTodo = () => {
  if (input_content.value.trim() === '') {
    return
  }
  todos.value.unshift({  // Use unshift to add to the top
    content: input_content.value,
    done: false,
    createdAt: new Date().getTime(),
    editing: false  // Add an editing state
  })
  input_content.value = ''
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

const toggleEdit = (todo) => {
  todo.editing = !todo.editing
}

// Save todos to localStorage whenever they change
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

// Load todos from localStorage when the component mounts
onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="create-todo">
      <h3>Create a Todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g. Make a video" v-model="input_content" />
        <input type="submit" value="Add todo" />
      </form>
    </section>
    
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`" :key="todo.createdAt">
          <label>
            <input type="checkbox" v-model="todo.done" />
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" :readonly="!todo.editing" />
          </div>
          <div class="actions">
            <button class="edit" @click="toggleEdit(todo)">{{ todo.editing ? 'Save' : 'Edit' }}</button>
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
