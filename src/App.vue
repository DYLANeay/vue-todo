<template>
  <form action="" @submit.prevent="addTodo">
    <fieldset>
      <input v-model="newTodo" type="text" placeholder="Ajouter une tâche" />
      <button :disabled="!newTodo.trim()" type="submit">Ajouter</button>
    </fieldset>
  </form>
  <div v-if="todos.length === 0">Vous n'avez pas de tâches à faire</div>
  <div v-else>
    <ul>
      <li v-for="todo in sortedTodos()" :key="todo.date" :class="{ completed: todo.completed }">
        <!-- If todo.completed === true, then we add the class completed-->
        <label> <input type="checkbox" v-model="todo.completed" />{{ todo.title }} </label>
      </li>
    </ul>
    <label><input type="checkbox" v-model="hideCompleted" />Masquer les tâches complétées</label>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const hideCompleted = ref(false); // This variable is not used in the template, but it can be used to filter the todos
const newTodo = ref('');
const todos = ref([
  {
    title: 'Tâche 1',
    completed: true,
    date: 1,
  },
  {
    title: 'Tâche 2',
    completed: false,
    date: 2,
  },
]);

const addTodo = () => {
  const trimmedTodo = newTodo.value.trim();
  if (trimmedTodo) {
    todos.value.push({
      title: trimmedTodo,
      completed: false,
      date: Date.now(),
    });

    newTodo.value = '';
  }
};

const sortedTodos = () => {
  const sortedTodo = [...todos.value].sort(
    //Spred operator to avoid mutating the original array, but creating a new one
    (a, b) => a.completed - b.completed,
  ); /*Sort by completed status, true is treated as 1
  false is treated as 0*/
  if (hideCompleted.value) {
    return sortedTodo.filter((todo) => todo.completed === false); //Filter the todos to only show the ones that are not completed
  } else {
    return sortedTodo;
  }
};
</script>

<style>
.completed {
  opacity: 0.5;
  text-decoration: line-through;
}
</style>
