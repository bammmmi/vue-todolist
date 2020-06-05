<template>
  <b-modal :ref="popupRef" hide-footer>
    <form ref="form" >
      <b-form-group>
        <b-form-input v-model="newTodo" placeholder="제목 입력" required :state="newTodo.length == 0"></b-form-input>
        <b-form-textarea
                v-model="content"
                placeholder="내용 입력"
                rows="3"
                max-rows="6"
        ></b-form-textarea>
        <b-form-datepicker id="datepicker-placeholder" placeholder="Choose a date" local="ko"  v-model="datepicker1"></b-form-datepicker>
      </b-form-group>
    </form>
    <b-button class="mt-3" variant="outline-danger"  @click="cancel">취소</b-button>
    <b-button class="mt-2" variant="outline-warning"  @click="addTodo">추가</b-button>
    <b-button class="mt-2" variant="outline-warning"  @click="modifyTodo">수정</b-button>
  </b-modal>
</template>

<script>
  export default {
    name: "TodoListModal",
    components: {
    },
    props: {
      popupRef: {
        type: String,
        required: true,
      },

      todo:{
        type: Object
      }
    },
    data() {
      return {
        newTodo : "",
        content : "",
        datepicker1: '',
        dated : false,
      }
    },
    mounted(){

    },
    computed: {

    },
    watch:{

    },
    methods: {
      show() {
        this.$refs[this.popupRef].show();
      },
      cancel() {
        this.$refs[this.popupRef].onCancel();
      },
      addTodo() {
        this.$emit('add',{newTodo : this.newTodo , content : this.content, date : this.datepicker1});
        this.$refs[this.popupRef].onCancel();
      },
      modifyTodo(){
        this.todo.title = this.newTodo;
        this.todo.content = this.content;
        this.todo.date = this.datepicker1;
        this.$emit('done', this.todo);
        this.$refs[this.popupRef].onCancel();
      }
    },
  }
</script>

<style >


</style>
