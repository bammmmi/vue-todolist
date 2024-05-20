<template>
  <b-modal :ref="popupRef" :title="modalTitle" hide-footer>

    <b-form-group>
      <b-row class="my-1">
        <b-col sm="3" class="pb-3">
          <label>* 제목 : </label>
        </b-col>
        <b-col sm="9" class="pb-3">
          <b-form-input v-model="title" placeholder="제목 입력" @keyup="validate"></b-form-input>
          <p v-if="valid" class="title-validate">제목을 입력해 주세요.</p>
        </b-col>


          <b-col sm="3">
              <label><b-form-checkbox v-model="dateYn">마감일 : </b-form-checkbox></label>
          </b-col>
          <b-col sm="9">
              <b-form-datepicker placeholder="마감일 선택" local="ko" v-model="date" :aria-disabled="!dateYn"></b-form-datepicker>
          </b-col>
      </b-row>

  </b-form-group>
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
      },
      modalTitle:{
        type: String
      }
    },
    data() {
      return {
        title : '',
        content : '',
        date: '',
        dateYn : false,
        rank : null,
        rankOptions: [
          { value: null, text: '우선순위 없음' },
          { value: '긴급', text: '긴급' },
          { value: '중요', text: '중요' },
        ],
        valid : '',
      }
    },
    mounted(){
    },
    watch:{
      todo: function() {
        this.title = this.todo.title;
        this.content = this.todo.content;
        this.rank = this.todo.rank;
        this.date = this.todo.date;
        this.date != '' ? this.dateYn = true : this.dateYn = false;
      },
      dateYn: function(){
        if(!this.dateYn){
          this.date = '';
        }else{
          this.date = this.todo.date;
        }
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
        this.validate();
        if(!this.valid) {
          if (!this.dateYn) {
            this.date = '';
          }
          this.$emit('add', {title: this.title, content: this.content, rank: this.rank, date: this.date});
          this.cancel();
        }
      },
      modifyTodo(){
        this.validate();
        if(!this.valid) {
          this.todo.title = this.title;
          this.todo.content = this.content;
          this.todo.rank = this.rank;
          if (!this.dateYn) {
            this.date = '';
          }
          this.todo.date = this.date;
          this.$emit('modifyDone', this.todo);
          this.cancel();
        }
      },
      validate(){
        this.valid = false;
        if(this.title.trim().length == 0){
          this.valid = true;
        }
      },
    },
  }
</script>

<style>
    .title-validate{
        color : #ff122d;
    }
</style>

