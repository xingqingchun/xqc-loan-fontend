<template>
  <div class="app-container">
    <eHeader :query="query"/>
    <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
      <el-table-column prop="subName" label="编码"/>
      <el-table-column prop="custName" label="客户"/>
      <el-table-column prop="totalInitAmount" label="初始总金额"/>
      <el-table-column prop="totalAddAmount" label="增加总金额"/>
      <el-table-column prop="totalLastAmount" label="最终总金额"/>
      <el-table-column prop="frozenInitAmount" label="初始冻结金额"/>
      <el-table-column prop="frozenAddAmount" label="增加冻结金额"/>
      <el-table-column prop="frozenLastAmount" label="最终冻结金额"/>
      <el-table-column prop="balanceInitAmount" label="初始可用金额"/>
      <el-table-column prop="balanceAddAmount" label="新增可用金额"/>
      <el-table-column prop="balanceLastAmount" label="最终可用金额"/>
      <el-table-column prop="startTime" label="开始时间"/>
      <el-table-column prop="endTime" label="结束时间"/>
      <el-table-column prop="statusText" label="状态"/>
      <el-table-column label="操作" width="150px" align="center">
        <template slot-scope="scope">
          <edit :data="scope.row" :sup_this="sup_this"/>
        </template>
      </el-table-column>
    </el-table>
    <!--分页组件-->
    <el-pagination
      :total="total"
      style="margin-top: 8px;"
      layout="total, prev, pager, next, sizes"
      @size-change="sizeChange"
      @current-change="pageChange"/>
  </div>
</template>

<script>
import initData from '@/mixins/initData'
import { parseTime } from '@/utils/index'
import eHeader from './module/header'
import edit from './module/edit'
import { doGet, doDictItem } from '@/utils/request'

export default {
  components: { eHeader, edit },
  mixins: [initData],
  data() {
    return {
      statusMap: [],
      delLoading: false, sup_this: this
    }
  },
  computed: {
    formatedData() {
      const data = []
      this.data.map(item => {
        this.statusMap.map(statusItem => {
          if (statusItem.id == item.status) {
            item.statusText = statusItem.label
          }
        })
        data.push(item)
      })
      return data
    }
  },
  created() {
    this.$nextTick(() => {
      this.init()
    })
    doDictItem(`Y00048`).then(res => {
      this.statusMap = res
    })
  },
  methods: {
    parseTime,
    beforeInit() {
      this.url = 'adjust/pastAccount/queryPage'
      this.params = this.query
      this.params.page = this.page
      this.params.size = this.size
      return true
    },
    subDelete(id) {
      this.delLoading = true
      doGet('adjust/pastAccount/delete?id=' + id).then(res => {
        this.delLoading = false
        this.$refs[id].doClose()
        this.init()
        this.$notify({
          title: '删除成功',
          type: 'success',
          duration: 2500
        })
      }).catch(err => {
        this.delLoading = false
        this.$refs[id].doClose()
        console.log(err.response.data.msg)
      })
    }
  }
}
</script>
<style scoped>
</style>
