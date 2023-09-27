<template>
  <div class="content" :class="themeClass">
    <div class="container">
      <div class="content__todo">
        <div class="todos">
          <div class="headline">
            <div class="title">
              <span>Todo</span>
            </div>
            <div class="theme__button">
              <button @click="toggleTheme">
                <i class="theme__icon"></i>
              </button>
            </div>
          </div>
          <form @submit.prevent="addTodo">
            <div class="field">
              <div class="control">
                <input v-model="todo" class="input" type="text" placeholder="Add..." />
              </div>
            </div>
            <button type="submit" class="btn btn-primary d-none">Add</button>
          </form>
          <div class="todos__shadow">
            <draggable v-model="filteredTodos" @end="onSortChange" :item-key="'id'" :element="'div'" ghost-class="ghost">
              <template #item="{ element }">
                <div class="todos__item" :key="element.id">
                  <div class="card">
                    <div class="card-content">
                      <div class="media">
                        <div class="media-left">
                          <input type="checkbox" :class="{ done: element.done }" @click="done(element)" name="checkbox" />
                        </div>
                        <div class="media-content">
                          <p :class="{ done: element.done }" @click="done(element)" class="title">{{ element.content }}</p>
                          <p class="subtitle">{{ element.done }}</p>
                        </div>
                      </div>
                    </div>
                    <button @click="deleteTodo(element)" class="delete"><i class="icon__delete"></i></button>
                  </div>
                </div>
              </template>
            </draggable>
            <div class="m__todos__button">
              <div class="remaining-items">
                {{ remainingTodoCount }} items left
              </div>
              <div class="clear">
                <button @click="clearCompleted">Clear Completed</button>
              </div>
            </div>
            <div class="todos__button">
              <div class="remaining-items">
                {{ remainingTodoCount }} items left
              </div>
              <div class="filters">
                <button @click="setFilter('all')" :class="{ active: filter === 'all' }">All</button>
                <button @click="setFilter('active')" :class="{ active: filter === 'active' }">Active</button>
                <button @click="setFilter('completed')" :class="{ active: filter === 'completed' }">Completed</button>
              </div>
              <div class="clear">
                <button @click="clearCompleted">Clear Completed</button>
              </div>
            </div>
          </div>
        </div>
        <div class="text">
          <p>Drag and drop to reorder list</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue';
import draggable from 'vuedraggable';

export default {
  components: {
    draggable,
  },
  setup() {
    const todo = ref('');
    const todos = ref([]);
    const isDarkMode = ref(false);
    const themeClass = computed(() => (isDarkMode.value ? 'dark-mode' : 'light-mode'));
    const filter = ref('all');
    let stopWatchingTodos;

    function addTodo() {
      todos.value.push({
        done: false,
        content: todo.value,
        id: Date.now(),
      });
      todo.value = '';
      saveTodos(); 
    }

    function done(todo) {
      todo.done = !todo.done;
      saveTodos(); 
    }

    function deleteTodo(todoToDelete) {
      todos.value = todos.value.filter(todo => todo !== todoToDelete);
      saveTodos(); 
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

      const storedTodos = localStorage.getItem('todos');
      if (storedTodos) {
        todos.value = JSON.parse(storedTodos);
      }
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

    function clearCompleted() {
      todos.value = todos.value.filter(todo => !todo.done);
      saveTodos(); 
    }

    function saveTodos() {
      localStorage.setItem('todos', JSON.stringify(todos.value));
    }

    function onSortChange(event) {
      const movedTodo = filteredTodos.value[event.oldIndex];
      filteredTodos.value.splice(event.oldIndex, 1);
      filteredTodos.value.splice(event.newIndex, 0, movedTodo);
      saveTodos();
    }

    stopWatchingTodos = watch(todos, () => {
      saveTodos();
    });

    onUnmounted(() => {
      stopWatchingTodos();
    });

    const remainingTodoCount = computed(() => {
      return todos.value.filter(todo => !todo.done).length;
    });

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
      clearCompleted,
      onSortChange,
    };
  },
};
</script>
