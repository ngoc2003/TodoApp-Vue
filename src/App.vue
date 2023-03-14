<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const content = ref('')
const category = ref(null)

const todosAsc = computed(() =>
  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt
  })
)

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

const addTodo = () => {
  if (!content.value.trim() || !category.value) {
    return
  }

  todos.value.push({
    content: content.value,
    category: category.value,
    createdAt: new Date().getTime(),
    isDone: false
  })

  content.value = ''
  category.value = null
}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  },
  {
    deep: true
  }
)
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  console.log(todos.value)
})
</script>
<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">Whats up, <input type="text" placeholder="Name here" v-model="name" /></h2>
    </section>
    <section class="create-todo">
      <form action="" @submit.prevent="addTodo">
        <h4>Whats on your todo list?</h4>
        <input type="text" placeholder="e.g. date with honey baby" v-model="content" />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category " value="personal" v-model="category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3 class="todo-list__title">YOUR TODO LIST</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todosAsc"
          :key="todo.content"
          :class="`todo-item ${todo.isDone && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.isDone" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
