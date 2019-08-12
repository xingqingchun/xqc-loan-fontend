<template>
  <div class="head-container">
    <dict-select
      v-model="query.subjectCode"
      placeholder="请选择科目"
      no="Y00049"
      style="width: 200px;"
      class="filter-item"
      @change="change_subjectCode"
      @keyup.enter.native="toQuery"/>
    <el-input v-model="query.custName" clearable placeholder="输入客户姓名" style="width: 200px;" class="filter-item" @keyup.enter.native="toQuery"/>
    <el-date-picker
      v-model="startTimeAll"
      class="filter-item"
      type="daterange"
      unlink-panels
      range-separator="至"
      start-placeholder="开始日期"
      end-placeholder="结束日期"/>

    <dict-select
      v-model="query.status"
      placeholder="请选择科目"
      no="Y00048"
      style="width: 200px;"
      class="filter-item"
      @change="change_status"
      @keyup.enter.native="toQuery"/>
    <el-button class="filter-item" size="mini" type="primary" icon="el-icon-search" @click="toQuery">搜索</el-button>
    <!-- 新增 -->
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
    change_status(val) {
      this.query.status = val
    },
    change_subjectCode(val) {
      this.query.subjectCode = val
    },
    toQuery() {
      this.query.startTime1 = this.startTimeAll[0].toString()
      this.query.startTime2 = this.startTimeAll[1].toString()
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
