<template>
  <div>
    <el-button  size="mini" type="success" @click="to">查看</el-button>
    <eForm ref="form" :id="data.id" :sup_this="sup_this" :is-add="false"/>
  </div>
</template>
<script>
import eForm from './form'
export default {
  components: { eForm },
  props: {
    data: {
      type: Object,
      required: true
    },
    sup_this: {
      type: Object,
      required: true
    }
  },
  methods: {
    to() {
      this.$refs.form.dialog = true
    }
  }
}
</script>

<style scoped>
  div{
    display: inline-block;
    margin-right: 3px;
  }
</style>


<style scoped>
  div{
    display: inline-block;
    margin-right: 3px;
  }
</style>
