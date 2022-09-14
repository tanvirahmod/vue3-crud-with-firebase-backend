<template>
  <div id="app">

    <h1 class="title has-text-centered">A Simple Todo App</h1>

    <form @submit.prevent="AddTodo">
      <div class="field is-grouped">
        <p class="control is-expanded">
          <input v-model="TodoText" class="input" type="text" placeholder="Find a repository">
        </p>
        <p class="control">
          <button :disabled="!TodoText" class="button is-info">
            Add
          </button>
        </p>
      </div>
    </form>

    <!-- card starts here -->
    <div v-for="(todo,index) in todos" :key="index" class="card mt-5">
      <div class="card-content">
        <div class="content">
          {{todo.text}}
        </div>
      </div>
      <footer class="card-footer">
        <a @click="popup(todo.id)" href="#" class="card-footer-item">
          Edit
        </a>
        <a @click="deleteTodo(todo.id)" href="#" class="card-footer-item">Delete</a>
      </footer>
    </div>

    <!-- edit popUp model -->
    <div class="modal" :class="{'is-active' : popupVar}">
      <div class="modal-background"></div>
      <div class="modal-card">
        <section class="modal-card-body">
          <!-- Content ... -->
          <form @submit.prevent="UpdateTodo">
            <div class="field is-grouped">
              <p class="control is-expanded">
                <input v-model="updateVal" class="input" type="text" placeholder="Find a repository">
              </p>
              <p class="control">
                <button :disabled="!updateVal" class="button is-info">
                  Update
                </button>
              </p>
            </div>
          </form>
        </section>

      </div>
    </div>

  </div>
</template>

<script setup>

import { ref, onMounted } from "vue"
import { collection, onSnapshot, addDoc, doc, deleteDoc, updateDoc,query, orderBy } from "firebase/firestore"; // get docs replaced to onsnapshoot for live data update
import { db } from "@/firebase"
// import { async } from "@firebase/util";



// a sinple javascript datas
const todos = ref([
  // {
  //   id:1,
  //   text:"Breakfast at 9",
  // },
  // {
  //   id:2,
  //   text:"Coding for 3 Hours",
  // }
])

// get data from firebase
onMounted(() => {
  // const querySnapshot = await getDocs(collection(db, "todos"));
  // const newTodo = []
  // querySnapshot.forEach((doc) => {
  //   // doc.data() is never undefined for query doc snapshots
  //   console.log(doc.id, " => ", doc.data());
  //   const todo = {
  //     id: doc.id,
  //     text: doc.data().content
  //   }
  //   newTodo.push(todo)
  // });
  // todos.value = newTodo

  // for descending order
  const allQueryInDesending = query(collection(db, "todos"), orderBy("date", "desc"));

  onSnapshot(allQueryInDesending, (querySnapshot) => {
    const newTodo = [];
    querySnapshot.forEach((doc) => {
      const todo = {
        id: doc.id,
        text: doc.data().content
      }
      newTodo.push(todo)
    });
    todos.value = newTodo
  });

})


const TodoText = ref('');


const AddTodo = () => {
  // let uniqueID = todos.value.length + 1;
  // const insertTodo = {
  //   id: uniqueID.toString(),
  //   text: TodoText.value
  // }
  // todos.value.unshift(insertTodo);

  addDoc(collection(db, "todos"), {
    content: TodoText.value,
    date: Date.now()

  });
  TodoText.value = "";
}


// delete totos
const deleteTodo = id => {
  deleteDoc(doc(db, "todos", id));
}


// for popUp open and close 
let popupVar = ref(false);
let updateVal = ref();
let index;
let uniqueID;

const popup = (id) => {
  popupVar.value = true;
  index = todos.value.findIndex(i => i.id === id);
  updateVal.value = todos.value[index].text;
  uniqueID = id;
}

// update todo
const UpdateTodo = () => {
  // todos.value[index].text = updateVal.value
  popupVar.value = false;


  updateDoc(doc(db, "todos", uniqueID), {
    content: updateVal.value
  });
}


</script>


<style>
@import "D:\NodeJS\vue3 projects\hsc_result\college-results\node_modules\bulma\css\bulma.min.css";

#app {
  max-width: 700px;
  margin: 0 auto;
  box-sizing: border-box;
  padding: 20px;

}
</style>
