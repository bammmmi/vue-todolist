<template>
  <b-modal :ref="popupRef" hide-footer>
    <b-row>
       제목 : <span class="w-100">{{ todo.title }}</span>
       내용 : <span class="w-100">{{ todo.content }}</span>
      <span v-if="todo.date != ''">마감일 : <span class="w-100">{{ todo.date }}</span></span>
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

<style >


</style>
