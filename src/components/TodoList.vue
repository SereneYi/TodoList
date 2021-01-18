<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="What needs to be done?"
      v-model="newTodo"
      v-on:keyup.enter="addTodo"
    />

    <div
      v-for="(todo, index) in todosFiltered"
      :key="todo.id"
      class="todo-item"
    >
      <div class="todo-item-left">
        <input
          type="checkbox"
          v-model="todo.completed"
          @change="doneEdit(todo)"
        />

        <div
          v-if="!todo.editing"
          @dblclick="editTodo(todo)"
          class="todo-item-label"
          :class="{ completed: todo.completed }"
        >
          {{ todo.title }}
        </div>
        <input
          v-else
          todo.editing="true"
          class="todo-item-edit"
          type="text"
          v-model="todo.title"
          @blur="doneEdit(todo)"
          @keyup.enter="doneEdit(todo)"
          @keyup.esc="cancelEdit(todo)"
          v-focus
        />
      </div>
      <div class="remove-item" @click="removeTodo(todo, index)">&times;</div>
    </div>

    <div class="second-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <div>{{ remaining }} items left</div>
    </div>
    <div class="second-container"></div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      idForTodo: "",
      beforeEditCache: "",
      filter: "all",
      todos: [],
    };
  },

  created(todos) {
    axios
      .get("http://localhost:21021/api/services/app/Todo/GetAll")
      .then((response) => {
        this.todos = response.data.result.items;
      })
      .catch((error) => {
        console.log(error);
      });

    todos;
  },

  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },

    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed);
      }
      return this.todos;
    },
  },

  directives: {
    focus: {
      // directive definition
      inserted: function (el) {
        el.focus();
      },
    },
  },

  methods: {
    addTodo(event) {
      event.preventDefault();
      axios
        .post("http://localhost:21021/api/services/app/Todo/Create", {
          id: this.idForTodo,
          title: this.newTodo,
          completed: false,
        })
        .then((response) => {
          this.todos = response.data.result.items;
        })
        .catch((error) => {
          console.log(error);
        });
      window.location.reload();
      if (this.newTodo.trim() == 0) {
        return;
      }

      if (this.newTodo.trim().length == 0) {
        return;
      }

      //  this.todos.push({
      //    id: this.idForTodo,
      //    title: this.newTodo,
      //    completed: false,
      //    editing: false,
      //  });
      //  this.newTodo = "";
      // // this.idForTodo = "";
    },

    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;

      const index = this.todos.findIndex((item) => item.id == todo.id);
      this.todos.splice(index, 1, {
        id: todo.id,
        title: todo.title,
        completed: todo.completed,
        editing: todo.editing,
      });
    },

    doneEdit(todo) {
      axios
        .put("http://localhost:21021/api/services/app/Todo/Update", {
          id: todo.id,
          title: todo.title,
          completed: todo.completed,
        })

        .then((response) => {
          this.todos = response.data.result.items;
        })
        .catch((error) => {
          console.log(error);
        });

      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;

      this.todos.splice("editTodo", {
        id: todo.id,
        title: todo.title,
        completed: todo.completed,
        editing: todo.editing,
      });
    },

    cancelEdit(todo) {
      todo.editing = false;
      todo.title = this.beforeEditCache;
    },

    removeTodo(todo, index) {
      axios
        .delete(
          "http://localhost:21021/api/services/app/Todo/Delete?Id=" + todo.id
        )
        .then((response) => {
          response.result.items.id;
        })
        .catch((error) => {
          console.log(error);
        });
      this.todos.splice(index, 1);
    },

    checkAllTodos() {
      this.todos.forEach((todo) => (todo.completed = event.target.checked));
    },
  },
};
</script>






<style >
body {
  background-color: rgb(255, 255, 255);
}

.todo-input {
  display: block;
  margin: 0;
  padding-top: 18px;
  padding-bottom: 18px;
  padding-left: 10%;
  color: inherit;
  width: 100%;
  border: none;
  transition: box-shadow var(--transitionDuration);
  box-shadow: 0.2rem 0.5rem 1.6rem rgb(161, 161, 161);
  font-size: x-large;
}

.todo-input:focus {
  outline: none;
  box-shadow: 0.2rem 0.5rem 1.6rem rgb(133, 132, 132);
}

.todo-input::placeholder {
  color: #d1d6d8;
  font-size: x-large;
}

.todo-item {
  display: flex;
  background-color: white;
  align-items: center;
  justify-content: space-between;
  border: none;
  transition: box-shadow var(--transitionDuration);
  box-shadow: 0.2rem 0.3rem 1rem rgb(161, 161, 161);
}

.remove-item {
  cursor: pointer;
  margin-right: 20px;
  font-size: 25px;
}

.edit-item {
  cursor: pointer;
  margin-left: 400px;
  font-size: 15px;
}

.remove-item:hover {
  color: black;
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 14px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 23px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.second-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  cursor: pointer;
  font-size: 14px;
  background-color: white;
  appearance: none;
  border-radius: 12px;
  border: 2px solid #9b9b9b;
}

button:hover {
  box-shadow: 0 12px 16px 0 rgba(0, 0, 0, 0.24),
    0 17px 50px 0 rgba(0, 0, 0, 0.19);
}

button:focus {
  outline: none;
}

.checkmark {
  margin-left: 20px;
}

.checkmark2 {
  margin-left: 18px;
}
</style>