<template>
  //test 
<div class="todoscreen">
  <div class="note">
    <div class="input-group input-group-lg">
      <div class="input-group-prepend">
        <span class="input-group-text" id="inputGroup-sizing-lg">ToDo</span>
      </div>
      <input type="text" class="form-control" aria-label="Large" aria-describedby="inputGroup-sizing-sm" v-model="newTodo" @keyup.enter="addTodo" placeholder="what needs to be done">
    </div>

    <transition-group enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
    <!--
    This is done with the $emit.
     <vue-todoItem class="todo-item" v-for="(todo, index) in todoFiltered" :key="todo.id" :todo="todo" :index="index" @removeTodo="removeItem" @finishedEdit="finishedEdit" :checkall="!anyRemaining">
-->
        <vue-todoItem class="todo-item" v-for="(todo, index) in todoFiltered" :key="todo.id" :todo="todo" :index="index"  :checkall="!anyRemaining">

      </vue-todoItem>
    </transition-group>
    <hr>
    <div class="summary">
      <div class="selectAll">
        <input type="checkbox" name="" value="" :checked="!anyRemaining" @change="toggleSelection"> Check All
      </div>
      <div class="left">
        Left Items: {{remeaning}}
      </div>
    </div>
    <hr>
    <div class="filters">
      <div class="">
        <button type="button" name="button" :class="{active:filter=='all'}" @click="filter='all'">All</button>
        <button type="button" name="button" :class="{active:filter=='active'}" @click="filter='active'">Active</button>
        <button type="button" name="button" :class="{active:filter=='completed'}" @click="filter='completed'">Completed</button>
      </div>
      <div class="">
        <button type="button" name="button" @click="clearCompleted" v-if="anyCompleted">Clear Completed</button>
      </div>
    </div>



  </div>
</div>
</template>


<script type="text/javascript">
import TodoItem from "../components/TodoItem.vue"

export default ({

  components: {
    'vue-todoItem': TodoItem,
  },


  data() {
    return {
      idForTodo: 3,
      newTodo: '',
      filter: 'all',
      todoList: [{
          id: 1,
          title: "This is the first todo",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "This is the first todo",
          completed: false,
          editing: false,
        },

      ]
    }
  },
  created(){
    eventBus.$on('removeTodo', (index)=>this.removeItem(index))
    eventBus.$on('finishedEdit', (data)=>this.finishedEdit(data))
  },
  computed: {
    remeaning() {
      return (this.todoList.filter(x => !x.completed)).length;
    },
    anyRemaining() {
      return this.remeaning != 0;
    },
    anyCompleted() {
      return (this.todoList.filter(x => x.completed)).length;
    },
    todoFiltered() {
      if (this.filter == 'all') {
        return this.todoList;
      } else if (this.filter == 'active') {
        return this.todoList.filter(x => !x.completed);
      } else if (this.filter == 'completed') {
        return this.todoList.filter(x => x.completed);
      } else {
        return this.todoList;
      }
    }
  },
  methods: {
    addTodo: function() {
      if (this.newTodo.length != 0) {
        let todo = {
          'id': this.idForTodo++,
          'title': this.newTodo,
          'beforeEditingtitle': '',
          'completed': false,
          'editing': false,
        };
        this.todoList.push(todo);
        this.todoentry++;
        this.newTodo = '';
      }
    },
    removeItem: function(data) {
      this.todoList.splice(data, 1);

    },

    doneEditMode: function(data) {

      if (data.title.trim().length == 0) {
        data.title = this.beforeEditingtitle;
      }
      data.editing = false;
    },

    cancelEdit: function(data) {
      data.title = this.beforeEditingtitle;
      data.editing = false;

    },
    toggleSelection: function() {

      this.todoList.forEach(x => x.completed = event.target.checked);
    },
    finishedEdit: function(data) {
      this.todoList.splice(data.index, 1, data.todo);

    },
    clearCompleted: function() {
      this.todoList = this.todoList.filter(x => !x.completed);
    }
  },





});
</script>

<style media="screen">
@import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css');

.todoscreen {
  width: 400px;
  margin: 0 auto;


}

.todo-item {
  margin-top: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.4s;

}

.list-label,
.selectAll {
  margin-left: 80px;
}

.list-label::before {
  content: ""
}

.close {

  display: block;

}

.completed {
  text-decoration: line-through;
}

.summary,
.filters {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

#completeChechk {
  margin-right: 5px;

}

.active {
  color: red;
  background-color: green;
}

.checked {
  color: blue;

}
</style>
