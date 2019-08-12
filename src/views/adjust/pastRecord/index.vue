<template>
  <div class="app-container">
    <eHeader :query="query"/>
    <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
      <el-table-column prop="batchCodeText" label="编码"/>
      <el-table-column prop="startTime" label="开始时间"/>
      <el-table-column prop="endTime" label="结束时间"/>
      <el-table-column prop="stateText" label="状态"/>
      <el-table-column prop="createTime" label="创建时间"/>
      <el-table-column prop="updateTime" label="更新时间"/>
      <el-table-column prop="errorMsg" label="错误信息"/>
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
      batchCodeMap: [],
      stateMap: [],
      delLoading: false, sup_this: this
    }
  },
  computed: {
    formatedData() {
      const data = []
      this.data.map(item => {
        this.batchCodeMap.map(batchCodeItem => {
          if (batchCodeItem.id == item.batchCode) {
            item.batchCodeText = batchCodeItem.label
          }
        })
        this.stateMap.map(stateItem => {
          if (stateItem.id == item.state) {
            item.stateText = stateItem.label
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
    doDictItem(`Y00049`).then(res => {
      this.batchCodeMap = res
    })
    doDictItem(`Y00048`).then(res => {
      this.stateMap = res
    })
  },
  methods: {
    parseTime,
    beforeInit() {
      this.url = '/adjust/pastRecord/queryPage'
      this.params = this.query
      this.params.page = this.page
      this.params.size = this.size
      return true
    },
    subDelete(id) {
      this.delLoading = true
      doGet('/adjust/pastRecord/delete?id=' + id).then(res => {
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
