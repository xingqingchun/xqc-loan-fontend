<template>
    <div class="app-container">
        <eHeader  :query="query"/>
        <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
                                    <el-table-column prop="sysTypeText" label="系统类别"/>
                        <el-table-column prop="code" label="日子编码"/>
                        <el-table-column prop="name" label="日志名称"/>
                        <el-table-column prop="statusText" label="状态"/>
                        <el-table-column prop="requestIp" label="请求ip"/>
                        <el-table-column prop="time" label="耗时"/>
                        <el-table-column prop="userName" label="用户"/>
                        <el-table-column prop="createTime" label="创建时间"/>
            <el-table-column label="操作" width="150px" align="center">
                <template slot-scope="scope">
                    <edit  :data="scope.row" :sup_this="sup_this"/>
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
    import { doGet,doDictItem } from '@/utils/request'



    export default {
        components: { eHeader, edit },
        mixins: [initData],
        data() {
            return {
                sysTypeMap: [],
                statusMap: [],
                delLoading: false, sup_this: this
            }
        },
    computed: {
        formatedData() {
            const data = []
            this.data.map(item => {
                this.sysTypeMap.map(sysTypeItem => {
                    if (sysTypeItem.id == item.sysType) {
                     item.sysTypeText = sysTypeItem.label
                   }
                 })
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
             doDictItem(`Y00078`).then(res => {
                this.sysTypeMap = res
             })
             doDictItem(`Y00079`).then(res => {
                this.statusMap = res
             })
        },
        methods: {
            parseTime,
            beforeInit() {
                this.url = '/system/log/queryPage'
                this.params = this.query
                this.params.page = this.page
                this.params.size = this.size
                return true
            },
            subDelete(id) {
                this.delLoading = true
                doGet('/system/log/delete?id=' + id).then(res => {
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
        let msg="系统异常";
        if(err.code!=undefined&&err.code!=null){
          msg=err.msg
        }
        else if(err.response.data!=undefined&&err.response.data!=null){
          msg=err.response.data.msg;
        }
        this.$notify({
          title: msg,
          type: 'error',
          duration: 2500
        })
        console.log(msg)
                })
            }
        }
    }
</script>
<style scoped>
</style>
