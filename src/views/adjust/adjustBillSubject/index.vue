<template>
    <div class="app-container">
        <eHeader  :query="query"/>
        <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
                                    <el-table-column prop="name" label="科目名称"/>
                        <el-table-column prop="code" label="科目编码"/>
                        <el-table-column prop="statusText" label="状态"/>
                        <el-table-column prop="createTime" label="创建时间"/>
                        <el-table-column prop="updateTime" label="更新时间"/>
                        <el-table-column prop="remark" label="备注"/>
            <el-table-column label="操作" width="150px" align="center">
                <template slot-scope="scope">
                    <edit  :data="scope.row" :sup_this="sup_this"/>
                    <el-popover
                            :ref="scope.row.id"
                            placement="top"
                            width="180">
                        <p>确定删除本条数据吗？</p>
                        <div style="text-align: right; margin: 0">
                            <el-button size="mini" type="text" @click="$refs[scope.row.id].doClose()">取消</el-button>
                            <el-button :loading="delLoading" type="primary" size="mini" @click="subDelete(scope.row.id)">确定</el-button>
                        </div>
                        <el-button slot="reference" :disabled="scope.row.id === 1" type="danger" size="mini">删除</el-button>
                    </el-popover>
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
             doDictItem(`Y002`).then(res => {
                this.statusMap = res
             })
        },
        methods: {
            parseTime,
            beforeInit() {
                this.url = '/adjust/adjustBillSubject/queryPage'
                this.params = this.query
                this.params.page = this.page
                this.params.size = this.size
                return true
            },
            subDelete(id) {
                this.delLoading = true
                doGet('/adjust/adjustBillSubject/delete?id=' + id).then(res => {
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