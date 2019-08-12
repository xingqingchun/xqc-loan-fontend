<template>
    <div class="app-container">
        <eHeader  :query="query"/>
        <el-table v-loading="loading" :data="formatedData" size="small" border style="width: 100%;">
                        <el-table-column prop="custName" label="姓名"/>
                        <el-table-column prop="typeText" label="类型"width="120px;"/>
                        <el-table-column prop="outId" label="外部业务" min-width="130px;"/>
                        <el-table-column prop="code" label="编码"width="150px;"/>
                        <el-table-column prop="validText" label="状态" width="50px;"/>
                        <el-table-column prop="url" label="请求地址" min-width="150px;"/>
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
                typeMap: [],
                validMap: [],
                delLoading: false, sup_this: this
            }
        },
    computed: {
        formatedData() {
            const data = []
            this.data.map(item => {
                this.typeMap.map(typeItem => {
                    if (typeItem.id == item.type) {
                     item.typeText = typeItem.label
                   }
                 })
                this.validMap.map(validItem => {
                    if (validItem.id == item.valid) {
                     item.validText = validItem.label
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
             doDictItem(`Y00068`).then(res => {
                this.typeMap = res
             })
             doDictItem(`Y00067`).then(res => {
                this.validMap = res
             })
        },
        methods: {
            parseTime,
            beforeInit() {
                this.url = '/cust/custInfo/queryPage'
                this.params = this.query
                this.params.page = this.page
                this.params.size = this.size
                return true
            },
            subDelete(id) {
                this.delLoading = true
                doGet('/cust/custInfo/delete?id=' + id).then(res => {
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
