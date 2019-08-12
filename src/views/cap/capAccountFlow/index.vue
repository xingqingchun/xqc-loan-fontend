<template>
  <el-dialog :append-to-body="true" :visible.sync="dialog" style="margin-left: 50px;"  width="1200px">
    <eHeader :query="query" @serach="doSerach"/>
    <div class="app-container">
      <!--表格渲染-->
      <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
        <el-table-column prop="inOutText" label="收支" width="60px;"/>
        <el-table-column prop="ioName" label="科目" width="170px;"/>
        <el-table-column prop="transAmount" label="金额" width="60px;"/>
        <el-table-column prop="flowTime" label="交易时间" width="140px;"/>
        <el-table-column prop="bakTotleAmount" label="交易前" width="70px;"/>
        <el-table-column prop="newTotleAmount" label="交易后" width="70px;"/>
        <el-table-column prop="statusText" label="状态"width="50px;"/>
        <el-table-column prop="isCheckText" label="对账" width="50px;"/>
        <el-table-column prop="remark" label="备注"/>
      </el-table>
      <!--分页组件-->
      <el-pagination
        :total="total"
        style="margin-top: 8px;"
        layout="total, prev, pager, next, sizes"
        @size-change="sizeChange"
        @current-change="pageChange"/>
    </div>
  </el-dialog>
</template>

<script>
import initData from '@/mixins/initData'
import { parseTime } from '@/utils/index'
import eHeader from './module/header'
import { doGet, doDictItem } from '@/utils/request'

export default {
  components: {eHeader},
  mixins: [initData],
  data() {
    return {
      inOutMap: [],
      statusMap: [],
      isCheckMap: [],
      delLoading: false, sup_this: this, dialog: false, accountId: ''
    }
  },
  computed: {
    formatedData() {
      const data = []
      this.data.map(item => {
        this.inOutMap.map(statusItem => {
          if (statusItem.id == item.inOut) {
            item.inOutText = statusItem.label
          }
        })
        this.statusMap.map(custTypeItem => {
          if (custTypeItem.id == item.status) {
            item.statusText = custTypeItem.label
          }
        })
        this.isCheckMap.map(custTypeItem => {
          if (custTypeItem.id == item.isCheck) {
            item.isCheckText = custTypeItem.label
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
    doDictItem(`Y00007`).then(res => {
      this.statusMap = res
    })
    doDictItem(`Y00045`).then(res => {
      this.inOutMap = res
    })
    doDictItem(`Y00007`).then(res => {
      this.isCheckMap = res
    })
  },
  methods: {
    parseTime,
    doInit() {
      this.$nextTick(() => {
        this.init()
      })
    },
    doSerach(){
      this.page = 0;
      this.init()
    },
    beforeInit() {
      this.url = 'cap/capAccountFlow/queryPage'
      this.params = this.query
      this.params.page = this.page
      this.params.size = this.size
      this.params.accountId = this.accountId
      return true
    }
  }
}
</script>
<style scoped>
</style>
