<script setup>
import { RouterLink, RouterView } from 'vue-router'
import {ref, onMounted, computed, watch } from 'vue'


const todos = ref([])
const name = ref('')
const selected_filter = ref('default')

const input_content = ref('')
const input_category = ref(null)
const input_dueDate = ref(null)


const todos_asc = computed(() => todos.value.slice().sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const todos_asc_date = computed(() => todos.value.slice().sort((a, b) => {
  return new Date(a.dueDate) - new Date(b.dueDate)
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  console.log("addTodo");
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    dueDate: input_dueDate.value,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
  input_dueDate.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch (todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(selected_filter, newVal => {
  localStorage.setItem('selected_filter', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  selected_filter.value = localStorage.getItem('selected_filter') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <!-- <VCalendar /> -->
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder = "Name here" v-model="name"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text"
          placeholder="e.g. do math hw"
          v-model="input_content" />
        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input 
            type="radio" 
            name="category" 
            value="school"
            v-model="input_category" />
            <span class="bubble school"></span>
            <div>School</div>
          </label>

          <label>
            <input 
            type="radio" 
            name="category" 
            value="personal"
            v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <h4>Pick a due date</h4>
        <div class="todo-date">
          <input 
          type="date" 
          class="date"
          :placeholder="input_dueDate"
          v-model="input_dueDate">
        </div>
        <!-- <VDatePicker v-model='input_dueDate' /> -->

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <select v-model="selected_filter">
        <option value="default">Sort by Creation Date</option>
        <option value="due-date">Sort by Due Date</option>
      </select>
      <div class="list">

        <div v-if="selected_filter === 'default'">
          <div v-for="todo in todos_asc" v-bind:key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
            
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.content">
            </div>

            <div class="todo-date">
              <input type="date" class="date" v-model="todo.dueDate">
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>

        <div v-else-if="selected_filter === 'due-date'">
          <div v-for="todo in todos_asc_date" v-bind:key="todo.dueDate" :class="`todo-item ${todo.done && 'done'}`">
            
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.content">
            </div>

            <div class="todo-date">
              <input type="date" class="date" v-model="todo.dueDate">
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>

        <div v-else>

        </div>

        

      </div>
    </section>

  </main>
</template>

<style scoped>

.todo-date {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.date {
  padding: 10px;
  border: 2px solid #4CAF50; /* Green border */
  border-radius: 5px; /* Rounded corners */
  font-size: 16px; /* Font size */
  transition: border-color 0.3s; /* Smooth transition for border color */
}

.date:focus {
  border-color: #66BB6A; /* Lighter green when focused */
  outline: none; /* Remove default outline */
}

</style>
