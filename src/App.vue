<script setup>
import { ref, onMounted, computed, watch } from "vue";
import { useToast } from "vue-toastification";


const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const toast = useToast();

// sort to-do lists
const todos_acs = computed(() => {
  todos.value.sort((a,b) => b.createAt - a.createAt );
})

// Add naming feature
watch(name , (newName) => {
  localStorage.setItem("name", newName);
})


// Add tasks to the To-do list
const handelAddTodo = () => {
  if(input_content.value.trim() === "" || input_category.value === null) {
    toast.error('Both fields must be filled.');
    return;
  }
  
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done:false,
    createAt: new Date().getTime(),
  })
  
}; 


watch(todos, (newTodos) => {
  localStorage.setItem("todos", JSON.stringify(newTodos))
}, {deep:true});


// remove todo 
const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

// mounting app
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos_acs.value = JSON.parse(localStorage.getItem("todos") )|| [];
});

</script>

<template>

  <main class="app">

    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="youre Name .." v-model="name"/> 
      </h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="handelAddTodo">
        <h4>What's on youre todo list?!</h4>
        <input type="text" placeholder="e.g. Make a Video" v-model="input_content">

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          
          <label>
            <input type="radio" name="category"  value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo">
      </form>
    </section>


    <main class="todo-list">
      <h3>TODO LIST :</h3>

      <div class="list" id="todo-list">
        <div  v-for="todo in todos" :key="todo.createAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
          

          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble  ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
          </label>
          
          <div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
        </div>
      </div>
    </main>


  </main>

</template>
