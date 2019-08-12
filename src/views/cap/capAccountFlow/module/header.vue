<template>
  <div class="head-container">
    <dict-select v-model="query.inOut" placeholder="选择收支" no="Y00045" style="width: 200px;" class="filter-item" @change="change_inOut" />
    <el-input v-model="query.ioCode" clearable placeholder="输入资金编码" style="width: 200px;" class="filter-item" @keyup.enter.native="toQuery"/>
    <el-date-picker
      placeholder="请选择交易时间"
      v-model="flowTimeAll"
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
      flowTimeAll : '',
      downloadLoading: false
    }
  },
  methods: {
     change_inOut(val) {
      this.query.inOut = val
    },
    toQuery() {
      if (this.flowTimeAll[0]!= null) { this.query.flowTime1 = this.flowTimeAll[0].toString() }
      if (this.flowTimeAll[1] != null) { this.query.flowTime2 = this.flowTimeAll[1].toString() }
      this.$emit('serach')
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
