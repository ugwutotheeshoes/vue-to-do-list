<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const content = ref("");
const category = ref(null);

const ascending = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);
const addTodo = () => {
  if (content.value.trim() === "" || content.value === null) {
    return;
  }

  todos.value.push({
    content: content.value,
    category: category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  content.value = "";
  category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hi, <input type="text" placeholder="name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TO DO LIST</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your to do list today?</h4>
        <input
          type="text"
          placeholder="e.g Buy some eggs"
          class="list"
          v-model="content"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add to do" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TO DO LIST</h3>
      <div class="lists">
        <div v-for="todo in ascending" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" class="list" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped></style>
