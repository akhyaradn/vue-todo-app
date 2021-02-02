<template>
  <div>
    <input class="todo-input" type="text" placeholder="Tasks" v-model="newTodo" @keyup.enter="addTodo">
    <br>
    <div class="todo-list" v-for="(todo,index) in filterTodos" :key="todo.id">
        <input type="checkbox" :checked="todo.completed" v-model="todo.completed">
        <div class="todo-item" :class="{ completed : todo.completed}" v-if="!todo.editing" @dblclick="editTodo(todo)" >{{todo.title}}</div>
        <input v-else type="text" class="todo-edit" v-model="todo.title" @keyup.esc="cancelEdit(todo)" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" v-focus>
      <div class="remove-todo" @click="removeTodo(index)" >&times;</div>
    </div>
    <div class="extra-container">
      <div><input type="checkbox" :checked="!anyRemaining" @click="checkAllTodos"> Check all</div>
      <div>{{ remaining }} Item(s) left</div>
    </div>
    <div class="extra-container">
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
    </div>
  </div>
</template>

<script>
  export default {
    name : 'todo-list',
    directives: {
      focus: {
        inserted: function (el) {
          el.focus()
        }
      }
    },
    data () {
      var data = {
        incrementId : null,
        beforeEditCache : null,
        newTodo : '',
        filter : 'all',
        todos : [
          {
            'id' : 1,
            'title' : 'First task',
            'completed' : false,
            'editing' : false
          },
          {
            'id' : 2,
            'title' : 'Buy groceries',
            'completed' : false,
            'editing' : false
          }
        ]
      }

      data.incrementId = data.todos.length + 1

      return data
    },
    computed: {
      remaining : function() {
        return this.filterTodos.filter(todo => !todo.completed).length
      },
      anyRemaining : function() {
        return this.remaining != 0
      },
      filterTodos: function() {
        if(this.filter == 'all') {
          return this.todos
        } else if(this.filter == 'completed') {
          return this.todos.filter(todo => todo.completed)
        } else {
          return this.todos.filter(todo => !todo.completed)
        }
      },
      showClearCompletedButton: function() {
        return this.todos.filter((todo) => todo.completed).length > 0
      }
    },
    methods: {
      addTodo: function() {
        if(this.newTodo.trim().length == 0) return

        this.todos.push({
          id: this.incrementId,
          title: this.newTodo,
          completed: false,
          editing : false
        })

        this.newTodo = '';
        this.incrementId++;
      },
      removeTodo: function(key) {
        this.todos.splice(key,1);
      },
      editTodo: function(todo) {
        this.beforeEditCache = todo.title
        todo.editing = true
      },
      doneEdit: function(todo) {
        if(todo.title.trim().length == 0) {
          todo.title = this.beforeEditCache
        }
        todo.editing = false
      },
      cancelEdit: function(todo) {
        todo.title = this.beforeEditCache
        todo.editing = false
      },
      checkAllTodos: function() {
        this.todos.forEach((todo) => todo.completed = event.target.checked)
      },
      clearCompleted: function() {
        this.todos = this.todos.filter(todo => !todo.completed)
      }
    }
  }
</script>

<style lang="scss">
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .todo-list {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }

  .remove-todo {
    cursor: pointer;
    margin-left: 14px;
    &:hover {
      color: black;
    }
  }

  .todo-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc; //override defaults
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .todo-item {
    width: 100%;
    text-align: left;
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  .active {
    background: lightgreen;
  }
</style>