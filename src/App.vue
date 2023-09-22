<template>
  <div class="content">
    <form @submit.prevent="addTodo">
      <div class="field">
        <label for="" class="label">Todo</label>
        <div class="control">
          <input v-model="todo" class="input border" type="text" placeholder="Lorem ipsum dolor sit amet." />
        </div>
      </div>
      <button type="submit" class="button">Add</button>
    </form>
    <div v-for="todo in todos" :key="todo.id" class="card my-5 mx-5">
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
  </div>
</template>

<script>
import { ref } from 'vue';
export default {
  setup() {
    const todo = ref('');
    const todos = ref([]);

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

    return {
      todo,
      todos,
      addTodo,
      done,
      deleteTodo,
    };
  },
};
</script>
