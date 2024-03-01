
<script setup>
import {ref, onMounted, computed, watch} from "vue"
import '@fortawesome/fontawesome-free/css/all.css'

const todos = ref([])
const input_content = ref("")
const todos_asc = computed(()=>todos.value.sort((a,b)=>{
  return b.createdAt - a.createdAt
}))

const addTodo = ()=>{
  if(input_content.value.trim() === ""){
    return 
  }
  todos.value.push({
    content: input_content.value,
    editable: false,
    createdAt :  new Date().getTime(),
    likes: 0, 
    dislikes:  0
  })
  input_content.value = "";
}
const removeTodo = (todo)=>{
  todos.value = todos.value.filter(t => t.createdAt !== todo.createdAt)
}

const toggleEditMode = (todo) => {
  todo.editable = !todo.editable;
};

const updateTodoContent = (todo, newContent) => {
  todo.content = newContent;
};

const likeTodo = (todo) => {
  todo.likes++;
};

const dislikeTodo = (todo) => {
  todo.dislikes++; 
};
function formatTime(timestamp) {
    const date = new Date(timestamp);
    const day = date.getDate().toString().padStart(2, '0')
    const month = (date.getMonth() + 1).toString().padStart(2, '0')
    const year = date.getFullYear().toString().padStart(2, '0')
    const hour = date.getHours().toString().padStart(2, '0')
    const minute = date.getMinutes().toString().padStart(2, '0')
    return `${day}.${month}.${year} -${hour}:${minute}`;
  }

watch(todos, (newValue)=>{
  localStorage.setItem("todos", JSON.stringify(newValue))
},{deep: true})
onMounted(()=>{
  todos.value = JSON.parse(localStorage.getItem("todos")) || []
})
</script>

<template>
  <main class="app">
  <section class="wrapper">
  <form @submit.prevent ="addTodo" class="content_area">    
    <textarea class="form-control main_content"  placeholder="Your text here" v-model="input_content"></textarea>
    <input class="sbmt_btn " type="submit" value="SUBMIT" aria-describedby="inputGroup-sizing-default">
  </form>

  <div class="card" v-for="todo in todos_asc">
  <div class="card-body" >
    <div class="above_content">
      <img class="profile_img" src="../src/images/profile.jpg">
      <div class="context">
        <div>{{ formatTime(todo.createdAt) }}</div>
        <textarea type="text" :value="todo.content" :readonly="!todo.editable" @input="updateTodoContent(todo, $event.target.value)"></textarea>
    </div>
  </div>
  <div class="line"></div>
  <div class="below_interaction">
      <div class="interaction">
        <button class="like" @click="likeTodo(todo)"><i class="fa-regular fa-thumbs-up"></i> {{ todo.likes }}</button>
        <button class="dislike" @click="dislikeTodo(todo)"> <i class="fa-regular fa-thumbs-down"></i> {{ todo.dislikes }}</button>
      </div>
   
      <div class="actions">
        <button class="edit" @click="toggleEditMode(todo)">
        <i v-if="todo.editable" class="fa-regular fa-floppy-disk"></i>
        <i v-else class="fa-solid fa-pen"></i>
      </button>
      <button class="delete" @click="removeTodo(todo)"><i class="fa-regular fa-trash-can"></i></button>
        </div>
      </div>
    </div>
    </div>
  </section>
  </main>
</template>
