<template>
    <div class="app-container">
        <eHeader  :query="query"/>
        <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
                                    <el-table-column prop="code" label="业务编码"/>
                        <el-table-column prop="bizId" label="业务ID" min-width="130px;"/>
                        <el-table-column prop="sendTime" label="发送时间"/>
                        <el-table-column prop="retTime" label="返回时间"/>
                        <el-table-column prop="retCode" label="返回编码"/>
                        <el-table-column prop="retMsg" label="返回信息"/>
                        <el-table-column prop="statusText" label="状态"/>
                        <el-table-column prop="createTime" label="创建时间"/>
                        <el-table-column prop="updateTime" label="更新时间"/>
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
             doDictItem(`Y00077`).then(res => {
                this.statusMap = res
             })
        },
        methods: {
            parseTime,
            beforeInit() {
                this.url = '/basic/sendRecviceLog/queryPage'
                this.params = this.query
                this.params.page = this.page
                this.params.size = this.size
                return true
            },
            subDelete(id) {
                this.delLoading = true
                doGet('/basic/sendRecviceLog/delete?id=' + id).then(res => {
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
