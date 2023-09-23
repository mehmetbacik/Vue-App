<template>
  <div class="content" :class="themeClass">
    <form @submit.prevent="addTodo">
      <div class="field">
        <label for="" class="label">Todo</label>
        <div class="control">
          <input v-model="todo" class="input border" type="text" placeholder="Lorem ipsum dolor sit amet." />
        </div>
      </div>
      <button type="submit" class="button">Add</button>
    </form>
    <div v-for="todo in filteredTodos" :key="todo.id" class="card my-5 mx-5">
      <div class="card-content">
        <div class="media">
          <div class="media-left"></div>
          <div class="media-content">
            <p :class="{done: todo.done}" @click="done(todo)" class="title cursor">{{todo.content}}</p>
            <p class="subtitle">{{todo.done}}</p>
          </div>
        </div>
      </div>
      <button @click="deleteTodo(todo)" class="delete">X</button>
    </div>
    <button @click="toggleTheme">Toggle Theme</button>

    <div class="remaining-items">
      {{ remainingTodoCount }} item(s) left
    </div>

    <div class="filters">
      <button @click="setFilter('all')" :class="{ active: filter === 'all' }">All</button>
      <button @click="setFilter('active')" :class="{ active: filter === 'active' }">Active</button>
      <button @click="setFilter('completed')" :class="{ active: filter === 'completed' }">Completed</button>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';

export default {
  setup() {
    const todo = ref('');
    const todos = ref([]);
    const isDarkMode = ref(false);
    const themeClass = computed(() => isDarkMode.value ? 'dark-mode' : 'light-mode');
    const filter = ref('all');

    function addTodo() {
      todos.value.push({
        done: false,
        content: todo.value,
        id: Date.now(),
      });
      todo.value = '';
    }

    function done(todo) {
      todo.done = !todo.done;
    }

    function deleteTodo(todoToDelete) {
      todos.value = todos.value.filter(todo => todo !== todoToDelete);
    }

    function toggleTheme() {
      isDarkMode.value = !isDarkMode.value;
      localStorage.setItem('isDarkMode', isDarkMode.value.toString());
    }

    onMounted(() => {
      const storedMode = localStorage.getItem('isDarkMode');
      if (storedMode === 'true') {
        isDarkMode.value = true;
      } else if (storedMode === 'false') {
        isDarkMode.value = false;
      }
    });

    const remainingTodoCount = computed(() => {
      return todos.value.filter(todo => !todo.done).length;
    });

    const filteredTodos = computed(() => {
      if (filter.value === 'all') {
        return todos.value;
      } else if (filter.value === 'active') {
        return todos.value.filter(todo => !todo.done);
      } else if (filter.value === 'completed') {
        return todos.value.filter(todo => todo.done);
      }
      return [];
    });

    function setFilter(newFilter) {
      filter.value = newFilter;
    }

    return {
      todo,
      todos,
      isDarkMode,
      addTodo,
      done,
      deleteTodo,
      toggleTheme,
      themeClass,
      remainingTodoCount,
      filter,
      setFilter,
      filteredTodos,
    };
  },
};
</script>
