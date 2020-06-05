<template>
  <b-modal :ref="popupRef" hide-footer>
    <form ref="form" >
      <b-form-group>
        <b-form-input v-model="title" placeholder="제목 입력"></b-form-input>
        <b-form-textarea
                v-model="content"
                placeholder="내용 입력"
                rows="3"
                max-rows="6"
        ></b-form-textarea>
        <b-form-checkbox v-model="dateYn">마감일 추가하기</b-form-checkbox>
        <b-form-datepicker placeholder="" local="ko"  v-model="date" :aria-disabled="!dateYn"></b-form-datepicker>
      </b-form-group>
    </form>
    <b-button class="mt-2" variant="outline-danger" @click="cancel">취소</b-button>
    <b-button class="mt-2" variant="outline-warning" @click="addTodo" v-if="mode=='add'">추가</b-button>
    <b-button class="mt-2" variant="outline-warning" @click="modifyTodo" v-else>수정</b-button>
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
      },
      mode:{
        type: String
      }
    },
    data() {
      return {
        title : '',
        content : '',
        date: '',
        dateYn : false,
      }
    },
    mounted(){
    },
    watch:{
      todo: function() {
        this.title = this.todo.title;
        this.content = this.todo.content;
        this.date = this.todo.date;
      }
    },
    methods: {
      show() {
        this.$refs[this.popupRef].show();
      },
      cancel() {
        this.$refs[this.popupRef].onCancel();
      },
      addTodo() {
        this.$emit('add',{title : this.title , content : this.content, date : this.date});
        this.cancel();
      },
      modifyTodo(){
        this.todo.title = this.title;
        this.todo.content = this.content;
        this.todo.date = this.date;
        this.$emit('done', this.todo);
        this.cancel();
      }
    },
  }
</script>

<style >


</style>
