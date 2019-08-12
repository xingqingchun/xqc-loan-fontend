<template>
  <div class="app-container">
    <eHeader :query="query"/>
    <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
      <el-table-column prop="custName" label="客户姓名" min-width="150px;"/>
      <el-table-column prop="totleAmount" label="总金额(元)" style="width:8%"/>
      <el-table-column prop="frozenAmount" label="冻结金额(元)" style="width:8%"/>
      <el-table-column prop="balanceAmount" label="可用余额(元)" style="width:8%"/>
      <el-table-column prop="statusText" label="状态" style="width:5%"/>
      <el-table-column prop="custTypeText" label="客户类型" style="width:5%"/>
      <el-table-column prop="errorTime" label="错误时间"style="width:10%"/>
      <el-table-column prop="errorMsg" label="错误信息" style="width:10%"/>
      <el-table-column prop="createTime" label="创建时间" style="width:10%"/>
      <el-table-column label="操作" width="250px" align="center">
        <template slot-scope="scope">
          <el-button slot="reference" size="mini" type="danger" @click="changeStatus(scope.row.id)">
            <span v-if="scope.row.status == 1">停用</span>
            <span v-if="scope.row.status == 0">启用</span>
          </el-button>
          <el-button size="mini" type="success" @click="viewFlow(scope.row.id)">账户流水</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!--分页组件-->
    <Item ref="item"/>
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
import {  doPost, doDictItem } from '@/utils/request'
import Item from './../capAccountFlow/index'

export default {
  components: { eHeader, Item },
  mixins: [initData],
  data() {
    return {
      statusMap: [],
      custTypeMap: [],
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
        this.custTypeMap.map(custTypeItem => {

          if (custTypeItem.id == item.custType) {
            item.custTypeText = custTypeItem.label
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
    doDictItem(`Y00006`).then(res => {
      this.statusMap = res
    })
    doDictItem(`Y00066`).then(res => {
      this.custTypeMap = res
    })
  },
  methods: {
    parseTime,
    viewFlow(id) {
      this.$refs.item.accountId = id
      this.$refs.item.dialog = true
      this.$refs.item.doInit()
    },
    changeStatus(id) {
      const url = 'cap/capAccount/updateStatus?id=' + id
      doPost(url).then(res => {
        this.loading = true
        this.$notify({
          title: '操作成功',
          type: 'success',
          duration: 2500
        })
        this.loading = false
        this.init()
      }).catch(err => {
        this.loading = false
        console.log(err.msg)
      })
    },
    beforeInit() {
      this.url = 'cap/capAccount/queryPage'
      this.params = this.query
      this.params.page = this.page
      this.params.size = this.size
      return true
    }
  }
}
</script>
<style scoped>
</style>
