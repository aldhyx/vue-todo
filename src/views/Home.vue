<template>
  <div class="container">
    <header>
      <img src="@/assets/logo.png" alt="logo" />
      <h1>Todo list</h1>
    </header>

    <main>
      <section class="todo-input">
        <input
          type="text"
          placeholder="What needs to be done?"
          v-model="newTodo"
          @keyup.enter="addTodo"
        />
        <button @click="addTodo">Add</button>
      </section>

      <section class="todo-header">
        <p>My Todo List</p>
        <p v-if="todos.length == 0">Nothing todo here!</p>
      </section>

      <transition-group
        name="fade"
        enter-active-class="animated fadeInUp"
        leave-active-class="animated fadeOutDown"
      >
        <div class="todo-item" v-for="(todo, index) in todosFiltered" :key="todo.id">
          <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed" />
            <div
              v-if="!todo.editing"
              class="todo-item-label"
              @dblclick="editTodo(todo)"
              :class="{ completed: todo.completed }"
              @click="toggleCompleted(todo)"
            >{{todo.title}}</div>
            <input
              v-else
              class="todo-item-edit"
              type="text"
              v-model="todo.title"
              @blur="doneEdit(todo)"
              @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-focus
            />
          </div>
          <div class="remove-item" @click="removeTodo(index)">&times;</div>
        </div>
      </transition-group>

      <section class="extra-container">
        <div>
          <input id="checkAll" @change="checkAllTodos" :checked="!anyRemaining" type="checkbox" />
          <label for="checkAll">Check All</label>
        </div>
        <div>{{ remaining }} items left</div>
      </section>

      <section class="extra-container">
        <div>
          <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
          <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
          <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
        </div>
        <div>
          <transition name="fade">
            <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
          </transition>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      idForTodo: 3,
      newTodo: '',
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          id: 1,
          title: 'Finish Vue Screencast',
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: 'Take over world',
          completed: false,
          editing: false
        }
      ]
    };
  },
  directives: {
    //   add custom directive
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == 'all') {
        return this.todos;
      } else if (this.filter == 'active') {
        return this.todos.filter(todo => !todo.completed);
      } else if (this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed);
      }

      return this.todos;
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },
  methods: {
    addTodo() {
      if (!this.newTodo) return;

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      });

      this.newTodo = '';
      this.idForTodo++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      if (!todo.title) {
        todo.title = this.beforeEditCache;
      }

      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    toggleCompleted(todo) {
      console.log(todo);
      if (todo.completed) {
        todo.completed = false;
      } else {
        todo.completed = true;
      }
    },
    checkAllTodos() {
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  }
};
</script>
<style lang="scss" scoped>
@import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css');

.container {
  width: 768px;
  margin: 0 auto;
  background-color: white;
  height: 100vh;
  overflow: auto;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.1);
  padding: 16px 32px;
  box-sizing: border-box;

  header {
    text-align: center;
    margin: 16px 0;

    img {
      width: 60px;
      margin-bottom: 16px;
    }

    h1 {
      color: #35495e;
      text-transform: uppercase;
      letter-spacing: 1px;
    }
  }

  main {
    margin-top: 32px;
  }

  // Todo Input
  .todo-input {
    position: relative;
    border: none;
    display: flex;

    input[type='text'] {
      font-size: 16px;
      width: 100%;
      border: 1px solid transparent;
      background-color: rgb(238, 238, 238);
      border-radius: 8px;
      padding: 16px;
      box-sizing: border-box;
      color: #35495e;
      transition: 0.5s;

      &:focus {
        outline: none;
        border-color: #41b883;
        background: #fff;
      }
    }

    button {
      margin-left: 16px;
      background-color: #41b883;
      border: none;
      border-radius: 8px;
      padding: 0 24px;
      text-transform: uppercase;
      color: #fff;
      font-weight: bolder;
      cursor: pointer;
      transition: 0.5s;

      &:hover {
        background-color: #35a371;
      }
      &:focus,
      &:active {
        outline: none;
      }
    }
  }
}

// Todo Header
.todo-header {
  margin-top: 32px;
  margin-bottom: 8px;
  p:nth-child(1) {
    font-size: 16px;
    font-weight: bold;
    color: #35495e;
  }

  p:nth-child(2) {
    font-size: 14px;
    font-weight: bold;
    margin-top: 16px;
    color: grey;
  }
}

// Todo-item
.todo-item {
  margin-bottom: 8px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;

  .todo-item-left {
    display: flex;
    align-items: center;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
    color: #35495e;
  }
  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;

    &:focus {
      outline: 0;
    }
  }

  .completed {
    text-decoration: line-through;
    color: grey !important;
  }

  .remove-item {
    align-self: center;
    cursor: pointer;
    margin-left: 12px;
    font-size: 16px;
    background-color: #eee;
    padding: 4px 9px;
    border-radius: 16px;
    transition: 0.5s;

    &:hover {
      color: black;
      background-color: rgb(202, 202, 202);
    }
  }

  input[type='checkbox']:focus {
    outline: none;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.extra-container {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid #eeeeee;
  padding-top: 24px;
  margin: 24px 0;
  color: #35495e;

  div {
    display: flex;
  }

  input[type='checkbox'] {
    margin-right: 16px;
  }

  button {
    margin-right: 8px;
    font-size: 14px;
    border: 1px solid #35495e;
    background: transparent;
    border-radius: 4px;
    padding: 8px 16px;
    font-weight: bold;
    color: #35495e;
    cursor: pointer;
    transition: 0.5s;

    &:hover {
      background-color: #35495e;
      color: #fff;
    }
    &:focus,
    &:active {
      outline: none;
    }
    &:last-child {
      margin-right: 0 !important;
    }
  }

  .active {
    background-color: #35495e;
    color: #fff;
  }
}

@media screen and (max-width: 767px) {
  .container {
    width: 100%;
    padding: 16px;
  }
}
</style>
