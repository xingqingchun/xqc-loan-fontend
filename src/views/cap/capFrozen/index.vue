<template>
  <div class="app-container">
    <eHeader  :query="query"/>
    <!--表格渲染-->
    <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
      <el-table-column prop="custName" label="客户姓名"/>
      <el-table-column prop="phone" label="电话"/>
      <el-table-column prop="frozenAmount" label="金额"/>
      <el-table-column prop="frozenIoName" label="冻结科目"/>
      <el-table-column prop="frozenTime" label="冻结时间"/>
      <el-table-column prop="frozenRemark" label="冻结摘要"/>
      <el-table-column prop="unfrozenIoName" label="解冻科目"/>
      <el-table-column prop="unfrozenTime" label="解冻时间"/>
      <el-table-column prop="unfrozenRemark" label="解冻摘要"/>
      <el-table-column prop="statusText" label="状态"/>
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
import eHeader from './module/header'
import { doGet , doDictItem} from '@/utils/request'
import Item from './../capFrozenFlow/index'



export default {
  components: { eHeader,Item},
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
    doDictItem(`Y00046`).then(res => {
      this.statusMap = res
    })
    this.$nextTick(() => {
      this.init()
    })
  },
  methods: {
    viewFlow(id){
      this.$refs.item.frozenId = id
      this.$refs.item.dialog = true
      this.$refs.item.doInit()
    },
    beforeInit() {
      this.url = 'cap/capFrozen/queryPage'
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
