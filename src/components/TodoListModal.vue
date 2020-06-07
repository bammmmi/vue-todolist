<template>
  <b-modal :ref="popupRef" :title="modalTitle" hide-footer>

    <b-form-group>
      <b-row class="my-1">
        <b-col sm="3" class="pb-3">
          <label for="title">제목 : </label>
        </b-col>
        <b-col sm="9" class="pb-3">
          <b-form-input id="title" v-model="title" placeholder="제목 입력" @keyup="validate"></b-form-input>
          <p v-if="valid">제목을 입력해 주세요.</p>
        </b-col>

          <b-col sm="3" class="pb-3">
              <label for="content">내용 : </label>
          </b-col>
          <b-col sm="9" class="pb-3">
            <b-form-textarea
                  id="content"
                  v-model="content"
                  placeholder="내용 입력"
                  rows="3"
                  max-rows="6"
            ></b-form-textarea>
          </b-col>

          <b-col sm="3" class="pb-3">
              <label for="rank">우선순위 : </label>
          </b-col>
          <b-col sm="9" class="pb-3">
              <b-form-select id="rank" v-model="rank" :options="rankOptions"></b-form-select>
          </b-col>

          <b-col sm="3">
              <label for="date"><b-form-checkbox v-model="dateYn">마감일 : </b-form-checkbox></label>
          </b-col>
          <b-col sm="9">
              <b-form-datepicker id="date" placeholder="마감일 선택" local="ko" v-model="date" :aria-disabled="!dateYn"></b-form-datepicker>
          </b-col>
      </b-row>

  </b-form-group>

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
          { value: null, text: '우선순위 설정' },
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
        if(this.date != '' ){
          this.dateYn = true;
        }
      },
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


</style>

