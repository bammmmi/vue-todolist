<template>
  <b-modal :ref="popupRef" title="Todo 상세" hide-footer>
    <b-row class="my-1">
      <b-col sm="3" class="pb-3">
        <label>제목 : </label>
      </b-col>
      <b-col sm="9" class="pb-3">
        <b-badge variant="danger" v-if="todo.rank == '긴급'">{{todo.rank}}</b-badge>
        <b-badge variant="info" v-if="todo.rank == '중요'">{{todo.rank}}</b-badge>
        {{ todo.title }}
      </b-col>
      <b-col sm="3" class="pb-3">
        <label>내용 : </label>
      </b-col>
      <b-col sm="9" class="pb-3">
        {{ todo.content }}
      </b-col>
      <b-col sm="3" v-if="todo.date != ''">
        <label>마감일 :</label>
      </b-col>
      <b-col sm="9" v-if="todo.date != ''">
        {{ todo.date }}
      </b-col>
    </b-row>
    <b-button class="mt-3" variant="outline-danger"  @click="cancel">확인</b-button>
  </b-modal>
</template>

<script>
  export default {
    name: "TodoListViewModal",
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
      }
    },
    mounted(){

    },
    methods: {
      show() {
        this.$refs[this.popupRef].show();
      },
      cancel() {
        this.$refs[this.popupRef].onCancel();
      },
    },
  }
</script>

<style>


</style>
