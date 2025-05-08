<template>
  <button @click="showTimer = !showTimer">Display / Hide</button>
  <Timer v-if="showTimer" />
  <Button>Entrez vos <strong>tâches</strong> ici</Button>
  <!-- <Layout>
    <template v-slot:header> En-tête </template>
    <template v-slot:aside> Barre latérale </template>
    <template v-slot:main> Contenu principal </template>
    <template v-slot:footer> Pied de page </template>
  </Layout> -->

  <form action="" @submit.prevent="addTodo">
    <fieldset>
      <input v-model="newTodo" type="text" placeholder="Ajouter une tâche" />
      <button :disabled="!newTodo.trim()" type="submit">Ajouter</button>
    </fieldset>
  </form>
  <div v-if="remainingTodos === 0"><p>Vous n'avez pas de tâches à faire</p></div>
  <div v-else>
    <ul>
      <li v-for="todo in sortedTodos" :key="todo.date" :class="{ completed: todo.completed }">
        <!-- If todo.completed === true, then we add the class completed-->
        <Checkbox
          :label="todo.title"
          @check="(p) => console.log('coché', p)"
          @uncheck="console.log('décoché')"
          v-model="todo.completed"
        />
      </li>
    </ul>
    <label><input type="checkbox" v-model="hideCompleted" />Masquer les tâches complétées</label>
    <p v-if="remainingTodos > 0">
      Vous avez encore <strong>{{ remainingTodos }}</strong> tâche{{
        remainingTodos > 1 ? 's' : ''
      }}
      à faire
    </p>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from 'vue';
import Checkbox from './Checkbox.vue'; // Importing the Checkbox component
import Button from './Button.vue'; // Importing the Button component
import Layout from './Layout.vue'; // Importing the Layout component
import Timer from './Timer.vue';

const hideCompleted = ref(false); // This variable is not used in the template, but it can be used to filter the todos
const newTodo = ref('');
const todos = ref([]);
const showTimer = ref(true);

onMounted(async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos');
    const data = await response.json();
    todos.value = data.map((todo) => ({ ...todo, date: todo.id })); // Fetching the todos from the API and linking the date to the id
  } catch (error) {
    console.error('Failed to fetch todos:', error);
  }
});

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

const sortedTodos = computed(() => {
  //Computed property helps not to recalculate the value if the dependencies are not changed (auto detects the dependencies)
  //It will only recalculate when todos or hideCompleted changes
  //This is a performance optimization
  const sortedTodo = [...todos.value].sort(
    //Spread operator to avoid mutating the original array, but creating a new one
    (a, b) => a.completed - b.completed,
  ); /*Sort by completed status, true is treated as 1
  false is treated as 0*/
  if (hideCompleted.value) {
    return sortedTodo.filter((todo) => todo.completed === false); //Filter the todos to only show the ones that are not completed
  }
  return sortedTodo;
});

const remainingTodos = computed(() => {
  return todos.value.filter((todo) => todo.completed === false).length; //Calculates the number of remaining todos
});
</script>

<style>
.completed {
  opacity: 0.5;
  text-decoration: line-through;
}
</style>
