<template>
  <div>
    <form @submit.prevent="addTodo">
      <input v-model="newTodo" />
      <button>Add Todo</button>
    </form>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <label v-if="!todo.edit">{{ todo.text }}</label>
        <input
          v-else
          type="text"
          v-model="editText"
          placeholder="Enter new name"
        />
        <button @click="removeTodo(todo)">X</button>
        <button v-if="!todo.edit" @click="toggleEdit(todo)">Edit</button>
        <button v-if="todo.edit" @click="changeTodoText(todo)">Submit</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

let id = "";

export default {
  data() {
    return {
      editText: "",
      newTask: "",
      todos: [],
    };
  },

  methods: {
    // onInput(e){
    //   this.newTask = e.target.value
    // },

    async getData() {
      try {
        const response = await axios.get("https://localhost:44338/api/Tasks");
        // JSON responses are automatically parsed.
        this.todos = response.data;
        console.log(response.data);
      } catch (error) {
        console.log(error);
      }
    },

    async postData(todo) {
      try {
        const response = await axios
          .post("https://localhost:44338/api/Tasks",todo);
        // JSON responses are automatically parsed.
        console.log(response);
      } catch (error) {
        console.log(error);
      }
    },

    
    addTodo() {
      id = getUID();

      var todo = { id: id, text: this.newTodo }

      this.todos.push(todo);

      this.postData(todo)

      console.log(id);
      this.newTodo = "";
    },

    removeTodo(todo) {
      console.log(todo.id);

      if (todo.edit) this.toggleEdit(todo);
      else this.todos = this.todos.filter((t) => t !== todo);
    },

    toggleEdit(todo) {
      if (!todo.edit) this.editText = "";
      console.log(todo.edit);
      this.todos = this.todos.map((t) => {
        console.log(todo);
        if (t === todo)
          return {
            id: t.id,
            text: t.text,
            edit: !t.edit,
          };
        else
          return {
            id: t.id,
            text: t.text,
            edit: t.edit == true ? false : t.edit,
          };
      });
    },

    changeTodoText(todo) {
      console.log(this.editText);
      console.log(todo.id);

      this.todos = this.todos.map((t) => {
        console.log(todo);
        if (t === todo)
          return {
            id: t.id,
            text: this.editText !== "" ? this.editText : t.text,
            edit: !t.edit,
          };
        else
          return {
            id: t.id,
            text: t.text,
            edit: t.edit,
          };
      });
      this.editText = "";
    },
  },

  created() {
    this.getData();
  },
};

function getUID() {
  var uniq = new Date().getTime();
  return uniq;
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
