<template>
  <div>
    <el-button :disabled="data.id === 1" size="mini" type="success" @click="to">编辑</el-button>
    <eForm ref="form" :data="data" @change="onChange" :is-add="false"/>
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
    }
  },
  methods: {
    to() {
      this.$refs['form'].dialog = true;
    },
    onChange(){
      this.$emit('change')
    }
  }
}
</script>

<style scoped>
  div {
    display: inline-block;
    margin-right: 3px;
  }
</style>
