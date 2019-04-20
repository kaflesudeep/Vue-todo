<template id="">
<div class="">
  <div class="list-label" @dblclick="editMode()" v-if="!editing" :class="{ completed:completed }">
    <input type="checkbox" id="completeChechk" :class="{checked:completed}" name="" value="" v-model="completed" @change="doneEditMode">{{title}}</div>
  <input v-else class="list-label" type="text" name="" value="" v-model="title" v-show="editing" @keyup.enter="doneEditMode()" @blur="doneEditMode()" @keyup.esc="cancelEdit()" v-focus>
  <div class="close" @click="removeItem(index)">&times; </div>

</div>
</template>
<script type="text/javascript">
export default ({
  name: 'vue-todoItem',
  directives: {
    focus: {
      // directive definition
      inserted: function(el) {
        el.focus()
      }
    }
  },
  props: {
    index: Number,
    todo: Object,
    checkall: Boolean,
  },
  data() {
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
      'beforeEditingtitle': '',

    }
  },
  watch: {
    checkall(){
      if(this.checkall){
        this.completed= true;
      }
        else{
          this.completed = this.todo.completed;
        }

    }
  },
  methods: {
    removeItem: function(index) {
      eventBus.$emit('removeTodo', index);

    },
    editMode: function() {
      this.beforeEditingtitle = this.title;
      this.editing = true;
    },

    doneEditMode: function(data) {

      if (this.title.trim().length == 0) {
        this.title = this.beforeEditingtitle;
      }
      this.editing = false;
      let todofinishededit = {
        'index': this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'beforeEditingtitle': '',
          'completed': this.completed,
          'editing': this.editing,
        }
      };

      eventBus.$emit('finishedEdit', todofinishededit);


    },

    cancelEdit: function() {
      this.title = this.beforeEditingtitle;
      this.editing = false;

    },
  }
});
</script>
