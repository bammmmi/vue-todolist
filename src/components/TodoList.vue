<template>
  <b-row class="todoapp">

    <span class="navbar w-100">Todo List</span>

    <b-row id="todolist" class="w-100">
       <b-alert variant="warning"
                class="w-100"
                dismissible
                fade
                :show="pendingAlert.length > 0 && todoList.length > 0"
                @dismissed="showDismissibleAlert = false"
       >
        마감기간이 지난 일정이 존재합니다.
      </b-alert>
      <b-row class="w-100">
        <b-row no-gutters align-h="between" align-v="center" class="pl-4 pr-3">
          <b-row no-gutters>
            <span class="badge">전체 : {{todoList.length}}</span>
            <span class="badge">완료 : {{completed || 0}}</span>
            <span class="badge">미 완료 : {{pending || 0}}</span>
          </b-row>
          <b-row no-gutters class="py-2">
            <b-button aria-label="Modify" title="Modify" class="btn-picto" @click="openTodoPopup">
              <span class="font-12">일정 추가하기</span>
            </b-button>
          </b-row>
        </b-row>
      </b-row>

      <b-row class="main w-100" v-show="todoList.length" v-cloak>
        <draggable v-model="todoList" group="info" @start="drag=true" @end="drag=false" class="w-100">
          <li v-for="todo in todoList"
                   class="todo-item"
                   :key="todo.id"
                   :class="{ done: todo.completed, undone: !todo.completed }">
            <div class="todo-info">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <b-badge variant="danger" v-if="todo.rank == '긴급'">{{todo.rank}}</b-badge>
              <b-badge variant="info" v-if="todo.rank == '중요'">{{todo.rank}}</b-badge>
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

        <b-row class="footer" v-show="todoList.length > pending" v-cloak>
          <b-button class="btn-picto" title="CompletedDelete" @click="removeCompleted" >
            완료 된 일정 삭제
          </b-button>
        </b-row>
      </b-row>

      <todo-list-modal
              ref="todoPopup"
              :todo="selectedTodo"
              :modalTitle="modalTitle"
              popup-ref="openTodoPopup"
              :mode="modalMode"
              @add="addTodo"
              @modifyDone="doneEdit"
              @onConfirmAction="addTodo"
      />

       <todo-list-view-modal
              ref="todoViewPopup"
              :todo="selectedTodo"
              popup-ref="openTodoViewPopup"
       />

    </b-row>

  </b-row>
</template>


<script>
  import draggable from "vuedraggable";
  import TodoListModal from "./TodoListModal"
  import TodoListViewModal from "./TodoListViewModal"
  import moment from "moment";
  import { uuid } from 'vue-uuid'

  export default {
    name: 'TodoList',
    components: {
      draggable,
      TodoListModal,
      TodoListViewModal
    },
    data () {
      return {
        todoList: [],
        editedTodo: null,
        drag : true,
        selectedTodo : {title: '', content: '', rank: null, date: ''},
        showDismissibleAlert : true,
        modalMode: null,
        modalTitle : ''
      }
    },
    watch: {
      todoList: {
        handler: function(updatedList) {
          localStorage.setItem('todoList', JSON.stringify(updatedList));
        },
        deep: true
      }
    },
    computed: {
      completed: function () {
        return _.filter(this.todoList, 'completed').length;
      },
      pending: function () {
        return _.filter(this.todoList, ['completed', false]).length;
      },
      pendingAlert: function () {
        return _.filter(this.todoList, function(t) {
          if (t.date != '') {
            if (moment(t.date).format('YYYY-MM-DD') < moment().format('YYYY-MM-DD') && !t.completed) {
              return t.date;
            }
          }
        });
      },
    },
    filters: {
      dateFormat: function(value, sep = '-') {
        if (!value) return "";
        return moment(value).format("YYYY-MM-DD");
      }
    },
    methods: {
      getTodoList() {
        if (localStorage.getItem('todoList')) {
          this.todoList = JSON.parse(localStorage.getItem('todoList'));
        }
      },
      addTodo(param) {
        this.todoList.unshift({
          id: uuid.v1(),
          title: param.title,
          content: param.content,
          date: param.date,
          rank: param.rank,
          completed: false
        });
      },
      removeTodo: function (todo) {
        this.todoList.splice(this.todoList.indexOf(todo), 1)
      },
      editTodo: function (todo) {
        this.modalMode = 'edit';
        this.modalTitle = 'Todo 수정하기';
        this.selectedTodo = todo;
        this.editedTodo = todo;
        this.$refs.todoPopup.show();
      },
      viewTodo: function(todo){
        this.selectedTodo = todo;
        this.$refs.todoViewPopup.show();
      },
      doneEdit: function () {
        if (!this.editedTodo) {
          return
        }
        this.editedTodo = null;
      },
      openTodoPopup () {
        this.modalMode = 'add';
        this.modalTitle = 'Todo 등록하기';
        this.selectedTodo = {title: '', content: '', rank: null, date: ''};
        this.$refs.todoPopup.show();
      },
      removeCompleted: function () {
        this.todoList = _.filter(this.todoList, ['completed', false]);
      },
    },

    mounted: function() {
      this.getTodoList();
    },
  }

</script>

<style scoped>

  .navbar {
    display: block;
    position: relative;
    font-size: 65px;
    font-weight: 100;
    text-align: center;
    color: rgba(59, 14, 18, 0.6);
    height: 100px;
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


  .todoapp {
    background: #fff;
    margin: 130px 0 40px 0;
    position: relative;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2),
    0 25px 50px 0 rgba(0, 0, 0, 0.1);
    height: 600px;
  }

  .todo-list li {
    position: relative;
    font-size: 24px;
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


</style>
