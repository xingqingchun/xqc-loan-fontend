<template>
  <div class="head-container">
    <el-input v-model="query.batchName" clearable placeholder="输入名称" style="width: 200px;" class="filter-item" @keyup.enter.native="toQuery"/>
    <dict-select v-model="query.batchCode" placeholder="请选择编码" no="Y00049" style="width: 200px;" class="filter-item" @change="change_batchCode" />
    <el-date-picker
      v-model="startTimeAll"
      placeholder="请选择开始时间"
      class="filter-item"
      type="daterange"
      unlink-panels
      range-separator="至"
      start-placeholder="开始日期"
      end-placeholder="结束日期"/>
    <el-button class="filter-item" size="mini" type="primary" icon="el-icon-search" @click="toQuery">搜索</el-button>
  </div>
</template>

<script>
import { parseTime } from '@/utils/index'
import eForm from './form'
import DictSelect from '../../../components/DictSelect'
// 查询条件
export default {
  components: { eForm, DictSelect },
  props: {
    query: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      startTimeAll: '',
      downloadLoading: false
    }
  },
  methods: {
    change_batchCode(val) {
      this.query.batchCode = val
    },
    toQuery() {
      this.query.startTime1 = null
      this.query.startTime2 = null
      if (this.startTimeAll[0] != null) { this.query.startTime1 = this.startTimeAll[0].toString() }
      if (this.startTimeAll[1] != null) { this.query.startTime2 = this.startTimeAll[1].toString() }
      this.$parent.page = 0
      this.$parent.init()
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => {
        if (j === 'createTime') {
          return parseTime(v[j])
        } else {
          return v[j]
        }
      }))
    }
  }
}
</script>
