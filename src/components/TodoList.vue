<template>
  <b-row class="todoapp">

    <span class="navbar w-100">Todo List</span>

    <b-row id="todolist" class="w-100">
      <b-alert
              variant="danger"
              dismissible
              fade
              :show="datetoto.length > 0 && showDismissibleAlert"
              @dismissed="showDismissibleAlert = false"
      >
        마감기간이 지난 일정이 존재합니다.
      </b-alert>
      <b-row class="w-100">
        <b-row no-gutters align-h="between" align-v="center" class="pl-4 pr-3">
          <b-row no-gutters>
            <span class="badge">전체 : {{todos.length}}</span>
            <span class="badge">완료 : {{completed || 0}}</span>
            <span class="badge">미완료 : {{pending || 0}}</span>
          </b-row>
          <b-row no-gutters class="py-2">
            <b-button aria-label="Modify" title="Modify" class="btn-picto" @click="openTodoPopup">
              <span class="font-12">추가하기</span>
            </b-button>
          </b-row>
        </b-row>
      </b-row>

      <b-row class="main w-100" v-show="todos.length" v-cloak>
        <draggable v-model="todos" group="info" @start="drag=true" @end="drag=false" class="w-100">
          <li v-for="todo in todos"
              class="todo-item"
              :key="todo.id"
              :class="{ done: todo.completed, undone: !todo.completed }">
            <div class="todo-info">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <span class="label todo-title">{{ todo.title }}</span>
            </div>
            <span class="todo-date">{{todo.date | dateFormat}}</span>

            <b-row class="actions">
              <b-button
                      @click="viewTodo(todo)"
                      aria-label="Detail"
                      title="Detail"
                      class="btn-picto"
              >
                <span class="font-12">상세</span>
              </b-button>
              <b-button
                      @click="editTodo(todo)"
                      aria-label="Edit"
                      title="Edit"
                      class="btn-picto"
              >
                <span class="font-12">수정</span>
              </b-button>

              <b-button
                      @click="removeTodo(todo)"
                      aria-label="Delete"
                      title="Delete"
                      class="btn-picto"
              >
                <span class="font-12">삭제</span>
              </b-button>
            </b-row>
          </li>
        </draggable>

        <b-row class="footer" v-show="todos.length > pending" v-cloak>
          <b-button class="btn-picto" title="CompletedDelete" @click="removeCompleted" >
            완료 된 일정 삭제
          </b-button>
        </b-row>
      </b-row>

      <todo-list-modal
              ref="todoPopup"
              :todo="todo"
              popup-ref="openTodoPopup"
              @add="addTodo"
              @done="doneEdit"
              @onConfirmAction="addTodo"
      />

    </b-row>

  </b-row>
</template>


<script>
  import draggable from "vuedraggable";
  import TodoListModal from "./TodoListModal"
  import moment from "moment";
  import { uuid } from 'vue-uuid'

  export default {
    name: 'TodoList',
    components: {
      draggable,
      TodoListModal
    },
    data () {
      return {
        todos: [],
        newTodo: null,
        content : null,
        editedTodo: null,
        visibility: 'all',
        drag : true,
        todo : null,
        datetoto : false,
        showDismissibleAlert : true
      }
    },
    watch: {
      todos: {
        handler: function(updatedList) {
          localStorage.setItem('todoList', JSON.stringify(updatedList));
        },
        deep: true
      }
    },
    computed: {
      completed: function () {
        return _.filter(this.todos, 'completed').length;
      },
      pending: function () {
        return _.filter(this.todos, ['completed', false]).length;
      },
    },
    filters: {
      dateFormat: function(value, sep = '-') {
        if (!value) return "";
        return moment(value).format("YYYY-MM-DD");
      }
    },
    methods: {
      getTodos() {
        if (localStorage.getItem('todoList')) {
          this.todos = JSON.parse(localStorage.getItem('todoList'));
        }
      },
      addTodo(param) {
        this.todos.unshift({
          id: uuid.v1(),
          title: param.newTodo,
          content: param.content,
          date: param.date,
          completed: false
        });
      },
      removeTodo: function (todo) {
        this.todos.splice(this.todos.indexOf(todo), 1)
      },
      editTodo: function (todo) {
        this.todo = todo;
        this.editedTodo = todo;
        this.$refs.todoPopup.show();

      },
      viewTodo: function(todo){
        this.todo = todo;
        this.$refs.todoPopup.show();
      },
      doneEdit: function (param) {
        if (!this.editedTodo) {
          return
        }
        this.editedTodo = null;
        param.title = param.title.trim();
        param.content = param.content.trim();
        param.date = param.date;
        if (!param.title) {
          this.removeTodo(param)
        }
      },
      openTodoPopup () {
        this.$refs.todoPopup.show();
      },
      removeCompleted: function () {
        this.todos = _.filter(this.todos, ['completed', false]);
      }
    },

    mounted: function() {
      this.getTodos();

      this.datetoto  = _.filter(this.todos, function(t){
        if(t.date != ''){
          if(moment(t.date).format('YYYY-MM-DD') < moment().format('YYYY-MM-DD') && !t.completed){
            return t.date;
          }
        }
      });
    },
  }

</script>

<style scoped>

  .navbar {
    display: block;
    position: relative;
    top: 10px;
    font-size: 65px;
    font-weight: 100;
    text-align: center;
    color: rgba(59, 14, 18, 0.6);
    height: 120px;
  }

  @keyframes strikeitem {
    to {
      width: calc(100% + 1rem);
    }
  }

  #todolist {
    padding: 2rem 3rem 3rem;
    background: #fff;
    color: #32325d;
    overflow: visible;
  }

  #todolist .emptylist {
    margin-top: 2.6rem;
    text-align: center;
    letter-spacing: 0.05em;
    font-style: italic;
    opacity: 0.8;
  }
  #todolist ul {
    margin-top: 1rem;
    list-style: none;
    padding: 0;
  }
  #todolist li {
    display: flex;
    margin-top: 5px;
    padding: 0.5rem 1rem;
    align-items: center;
    background-color: rgba(255, 255, 255, 0.1);
    text-align: left;
    padding-left: 5px;
  }

  #todolist .label {
    position: relative;
    transition: opacity 0.2s linear;
  }
  #todolist .label.todo-title {
    color: #7a797e;
  }

  #todolist .done .label:before {
    content: "";
    position: absolute;
    top: 50%;
    left: -0.5rem;
    display: block;
    width: 0%;
    height: 1px;
    background: #fff;
    animation: strikeitem 0.3s ease-out 0s forwards;
  }
  #todolist .btn-picto {
    border: none;
    background: none;
    -webkit-appearance: none;
    cursor: pointer;
    color: #11cdef;
  }

  .togglebutton-wrapper label {
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }
  .togglebutton-wrapper input {
    position: absolute;
    left: -9999px;
  }

  .todo-info {
    flex: 1 70%;
  }
  .todo-date {
    font-size: 12px;
    color: #8898aa;
    flex: 1 10%;
  }

  #todolist li.todo-item:hover {
    background-color: #f4f5f7;
    cursor: pointer;
  }
  .todo-item .handle-wrapper {
    width: 20px;
    color: #b5b5b5;
    opacity: 0;
  }
  #todolist li.todo-item:hover .handle-wrapper {
    opacity: 1;
  }
  .handle-wrapper:hover {
    cursor: move;
  }

  .todoapp {
    background: #fff;
    margin: 130px 0 40px 0;
    position: relative;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2),
    0 25px 50px 0 rgba(0, 0, 0, 0.1);
    height: 600px;
  }



  .main {
    position: relative;
    z-index: 2;
    border-top: 1px solid #e6e6e6;
  }

  .todo-list li {
    position: relative;
    font-size: 24px;
    border-bottom: 1px solid #ededed;
  }

  .todo-list li:last-child {
    border-bottom: none;
  }

  .todo-list li label {
    word-break: break-all;
    padding: 15px 15px 15px 60px;
    display: block;
    line-height: 1.2;
    transition: color 0.4s;
  }

  .todo-list li.completed label {
    color: #d9d9d9;
    text-decoration: line-through;
  }

  .footer {
    color: #777;
    padding: 10px 15px;
    height: 20px;
    text-align: center;
  }

  .footer:before {
    position: absolute;
    right: 0;
    bottom: 0;
    left: 0;
    height: 50px;
    overflow: hidden;
  }

  .filters {
    margin: 0;
    padding: 0;
    list-style: none;
    position: absolute;
    right: 0;
    left: 0;
  }

  .filters li {
    display: inline;
  }

  .filters li a {
    color: inherit;
    margin: 3px;
    padding: 3px 7px;
    text-decoration: none;
    border: 1px solid transparent;
    border-radius: 3px;
  }

  .filters li a:hover {
    border-color: rgba(175, 47, 47, 0.1);
  }

  .filters li a.selected {
    border-color: rgba(175, 47, 47, 0.2);
  }

  .clear-completed,
  html .clear-completed:active {
    float: right;
    position: relative;
    line-height: 20px;
    text-decoration: none;
    cursor: pointer;
  }


  #todolist .done .label{
    opacity: 0.6;
  }
  #todolist .done .label:before {
    content: "";
    position: absolute;
    top: 50%;
    left: -0.5rem;
    display: block;
    width: 0%;
    height: 1px;
    background: #fff;
    animation: strikeitem 0.3s ease-out 0s forwards;

  }

</style>
