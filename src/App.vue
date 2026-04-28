
 <script setup>
import { ref, onMounted, computed, watch } from 'vue'

const name = ref('')
const input_content = ref('')
const todos = ref([])
const input_category = ref(null)

const todo_asc = computed(() => {
  return [...todos.value].sort((a, b) => b.createAt - a.createAt)
})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || '[]')
})

const createTarea = () => {
  if (input_content.value.trim() === '' || input_category.value === null) return

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createAt: new Date().getTime(),
    done: false
  })


  input_content.value = ''
  input_category.value = null
}
const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
</script>
 


<template>
<main class="app">
<section class="greeting">
    <h2 class="title"> Hola ¡Que bueno verte! <input type="text" placeholder="name here" v-model="name">


    </h2>
</section>

<section class="create-tarea">
    <h3>Creemos una Tarea </h3>
<form @submit.prevent="createTarea">
    <h4>¿Que hay en tu lista de tareas?</h4>
    <input type="text" placeholder="e.g make a video"
    v-model="input_content">
    <h4>Categoria</h4>

    <div class="options">
        <label>
            <input type="radio" 
            name="category" 
            id="category1" 
            value="business" 
            v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
        </label>

 <label>
            <input type="radio" 
            name="category" 
            id="category1" 
            value="personal" 
            v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
        </label>
    </div>
    <input type="submit" value="añadir tarea">
</form>
</section>
<section class="tarea-pendiente">
    <h3>Lista de Tareas Pendientes</h3>
    <div class="list">
      <div v-for="todo in todo_asc" :key="todo.createAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
          <label >
            <input type="checkbox" v-model="todo.done"/>
            <span :class=" `bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content"/>
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Eliminar</button>
          </div>
      </div>
    </div>

</section>


</main>
</template>
<style>
/* Reset básico */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Segoe UI', sans-serif;
}

/* Fondo general */
body {
  background: linear-gradient(135deg, #ffe4ec, #ffd6e7);
  color: #4a4a4a;
}

/* Contenedor principal */
.app {
  max-width: 600px;
  margin: 40px auto;
  background: #fff0f6;
  padding: 25px;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(255, 105, 180, 0.2);
}

/* Títulos */
h2, h3, h4 {
  margin-bottom: 10px;
  color: #d63384;
}

/* Input nombre */
.title input {
  border: none;
  background: transparent;
  border-bottom: 2px solid #ff85c1;
  outline: none;
  font-size: 18px;
  color: #d63384;
  margin-left: 10px;
}

/* Inputs generales */
input[type="text"] {
  width: 100%;
  padding: 10px;
  border: 2px solid #ffc0cb;
  border-radius: 10px;
  margin-bottom: 15px;
  outline: none;
  transition: 0.3s;
}

input[type="text"]:focus {
  border-color: #ff69b4;
}

/* Botón */
input[type="submit"] {
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: 12px;
  background: #ff69b4;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
}

input[type="submit"]:hover {
  background: #ff1493;
}


/* eliminar */
button.delete {
  background: linear-gradient(135deg, #ff85c1, #ff4d94);
  border: none;
  color: white;
  padding: 8px 14px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 10px rgba(255, 105, 180, 0.3);
}



/* Opciones */
.options {
  display: flex;
  gap: 20px;
  margin-bottom: 15px;
}

.options label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

/* Burbujas */
.bubble {
  width: 15px;
  height: 15px;
  border-radius: 50%;
}



/* Lista */
.list {
  margin-top: 20px;
}

/* Item */
.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #ffe4ec;
  padding: 12px;
  border-radius: 12px;
  margin-bottom: 10px;
  transition: 0.3s;
}

.todo-item:hover {
  transform: scale(1.02);
}

/* Texto */
.todo-content input {
  border: none;
  background: transparent;
  font-size: 16px;
}

/* Checkbox */
input[type="checkbox"] {
  accent-color: #ff69b4;
}

/* Tarea completada */
.todo-item.done {
  opacity: 0.6;
  text-decoration: line-through;
}

/* Responsive */
@media (max-width: 600px) {
  .app {
    margin: 20px;
  }
}
</style>