<template>
  <main class="app">
    <section class="greeting">
      <h2>
        <!-- What's up, <input type="text" placeholder="name here" v-model="name" /> -->
      </h2>
    </section>

    <div class="container">
    <section>
      <h1>CREATE A TO DO</h1>
      <h2> What needs to be done? </h2>
      <input class="inputTasks"
        type="text"
        placeholder="e.g. study"
        v-model="input_content"
      />

      <h2> Pick a category </h2>
      <div class="options">
        <label class="inline">
          <input
            type="radio"
            name="category"
            value="business"
            v-model="input_category"
          />
          <span class="bubble-business"></span>
          <div>Business</div>
        </label>

        <label class="inline">
          <input
            type="radio"
            name="category"
            value="personal"
            v-model="input_category"
          />
          <span class="bubble-personal"></span>
          <div>Personal</div>
        </label>
      </div>

      <input class="buttonAdd" type="submit" value="Add to do" @click="addTodo" />
    </section>

    <section class="todo-list">
      <h2>Here's what you gotta do:</h2>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :key="todo.createdAt"
          :class="['todo-item', { done: todo.done }]"
        >
          <label class="task-container">
            <input class="task-checkbox" type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${
                todo.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>

            <div class="todo-content">
              <input class="inputTasks" style="margin-bottom: 10px; margin-top: 10px;" type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </label>
        </div>
      </div>
    </section>
   </div>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => {
  return todos.value.slice().sort((a, b) => b.createdAt - a.createdAt);
});

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  },
  { deep: true }
);

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    alert('Please provide both content and a category.');
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  // Reset input fields
  input_content.value = '';
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  const storedTodos = JSON.parse(localStorage.getItem('todos') || '[]');
  todos.value = storedTodos;
});
</script>


