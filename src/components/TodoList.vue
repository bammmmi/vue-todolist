<template>
  <div>
    <button @click="detailTodo">상세</button>
    <todo-list-modal />
  </div>
</template>


<script>
  import todoListModal from "./TodoListModal"

  export default {
    name: 'TodoList',
    components: {
      todoListModal
    },
    data () {
      return {

      }
    },
    watch: {

    },
    computed: {

    },
    filters: {

    },
    methods: {
      detailTodo: function(todo){
        this.$modal.show('todo-detail');
      },
    },
    directives: {

    },
    mounted: function() {
    },
  }

</script>

<style scoped>
</style>