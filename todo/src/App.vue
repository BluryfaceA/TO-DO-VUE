
<script setup>

/** REF --> Se utiliza para crear un valor reactivo que puede ser un valor primitivo (número, cadena, booleano) o un objeto.*/
/** onMounted  --> PARA EL LOCAL STORRE*/
/** WATCH --> QUE DETECTE CUALQUIER CAMBIO DE VARIABLE O EN EL LOCAL STORE, SE ACTUALICEN*/
import{ref, onMounted,computed,watch} from 'vue'

//*array vacio/
const todos = ref([])
const nombre = ref('')
const Filtro = ref('')


const input_content = ref(null)
const input_category= ref(null)


const Titulo = ref('')
const Descripcion  = ref('')


// Propiedad computada para filtrar tareas con categoría 'Personal'
const displayedTodos = computed(() => {
  return Filtro.value ? filteredTodos.value : todos.value;
});
const filteredTodos = computed(() => {
  if (Filtro.value === 'true') {
    return todos.value.filter(todo => todo.Done === true);
  } else if (Filtro.value === 'false') {
    return todos.value.filter(todo => todo.Done === false);
  } else {
    return todos.value; // Si no hay filtro, muestra todas las tareas
  }
});


const FiltrarDone = computed(() => {
  return todos.value.filter(todo => todo.Done === true);
});

//Guardar Nuestro Nombre en el LocalStore

 watch(nombre, (newNombre)=>{
  localStorage.setItem('name', newNombre)
})

// El deep lo que hace es ver en el localStore , donde es donde estamos guardando nuestros datosn en el array, si hay un cambio verfica y actualiza

 watch(todos, (newValor)=>{
  localStorage.setItem('todo', JSON.stringify(newValor))
},{
  deep:true
})

const addTodo = ()=>{

if(Titulo.value.trim() === '' || Descripcion.value.trim()=== ''  || input_category.value === null    ){
 return console.log("Rellena Todos los Campos ")
}else{
  todos.value.push({
  Tit: Titulo.value,
  Contenido: Descripcion.value,
  Categoria: input_category.value,
  Done: false,
  Fecha: new Date().getTime()



})
Titulo.value = ''
Descripcion.value = ''
}
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

const remove = () => {
	Filtro.value = ''
}


onMounted(() => {
	nombre.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todo')) || []
})

</script>

<template>

<h1>Prueba Empresa</h1>
   

  
<main class="app">

<div class="pag1">
  <section class="greeting">

<h2 class="title">
  Hola!, <input type="text" placeholder="Ingresa Tu Nombre" v-model="nombre">  
</h2>

</section>

<section class="create-todo">

<h3>Ingresar una Tarea:</h3>
<form @submit.prevent="addTodo">

<h4>Que te Gustaría Añadir?</h4>
<h3>Titulo de tu Nueva Tarea:</h3>
<input type="text" placeholder="ejm. Aprender Vue" v-model="Titulo"> 

<h3>Descripción de la Tarea:</h3>
<input type="text"  v-model="Descripcion"> 



<h4>Escoga una Categoría</h4>
<div class="options">

<label >
<input type="radio" name="category"  id="category1"  value="Negocios" v-model="input_category">
<span class="bubble business"></span>
<div>Negocios</div>
</label>

<label>

<input type="radio" name="category" id="category2"  value="personal" v-model="input_category">
<span class="bubble personal"></span>
<div>Personal</div>

</label>

</div>

<input type="submit" value="Añadir Tarea">

</form>
</section>

<section class="todo-list">
<h3>Listado de Mis Tareas:</h3>
<h4>Para Editar Sobreescriba</h4>
<div class="list">

<div v-for="todo in displayedTodos" :key="todo.id"  :class="`todo-item ${todo.Done && 'done'}`">
     
  <label>
        <input type="checkbox" v-model="todo.Done" />
        <span :class="`bubble ${todo.Categoria === 'Negocios' ? 'Negocios' : 'personal'}`"></span>
  </label>
    
  <div class="todo-content">

    <input type="text" id="titulo" v-model="todo.Tit"/> <br>
    <input type="text" id="titulo" v-model="todo.Contenido"/>

  </div>

  <div class="actions">
    <button class="delete" @click="removeTodo(todo)">Delete</button>
  </div>
</div>


</div>
</section>
</div>

<div class="pag2">
  
  <h2 class="title">
  Filtrar Por:
  </h2>


<div class="filtres">

    <label >
          <input type="radio" name="category"  id="personalTodos"  value="true" v-model="Filtro">
          <span class="bubble categoria1"></span>
          <div>Echas</div>
    </label>

    <label > 
          <input type="radio" name="category"  id="NegociosTodos"  value="false" v-model="Filtro">
          <span class="bubble categoria2"></span>
          <div>Pendientes</div>
    </label>


    <button class="Eliminar" @click="remove()">Quitar Filtro(s)</button>

</div>


</div>





</main>





</template>

<style scoped>
 

#titulo{
  font-weight: bold;
}
 .app {
  display: flex;
  gap: 20px; /* Espacio entre las columnas */
  margin-top:25px ;
}


.pag1{
  flex: 1; /* Permite que ambas secciones ocupen el mismo ancho */
  padding: 20px; /* Espacio interno */
  border: 1px solid #ddd; /* Borde ligero */
  border-radius: 8px; /* Bordes redondeados */
}

.pag1 {
  background-color: white; /* Color de fondo */
}

.pag2 {
  background-color: white; /* Color de fondo */
  padding: 100px;
  border: 1px solid #ddd; /* Borde ligero */
  border-radius: 8px; /* Bordes redondeados */
  height: 400px;
}



 .Eliminar{
  color: white;
  background: red;
 }



/* Estilo para el span que indica la categoría */
.bubble {
  width: 14px;
  height: 14px;
  display: inline-block;
  margin-right: 10px; /* Espacio entre el círculo y el texto */
}



/* Estilo para el botón de eliminar filtro */
.Eliminar {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #FF5722; /* Color de fondo naranja */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

/* Estilo para el botón de eliminar filtro cuando se pasa el mouse por encima */
.Eliminar:hover {
  background-color: #E64A19; /* Color de fondo más oscuro al pasar el mouse */
}

</style>



