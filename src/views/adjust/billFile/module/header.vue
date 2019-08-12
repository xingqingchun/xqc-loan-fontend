<template>
    <div class="head-container">
                            <dict-select v-model="query.payType" placeholder="请选择支付类型" no="Y01" style="width: 200px;" class="filter-item"  @change="change_payType" />
                    <el-input v-model="query.channelCode" clearable placeholder="输入渠道编码" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                    <dict-select v-model="query.status" placeholder="请选择状态 0未对账 1已对账 9对账不平" no="Y02" style="width: 200px;" class="filter-item"  @change="change_status" />
                  <el-date-picker
                    placeholder="请选择账单日期"
                    v-model="billDateAll"
                    class="filter-item"
                    type="daterange"
                    unlink-panels
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"/>
        <el-button class="filter-item" size="mini" type="primary" icon="el-icon-search" @click="toQuery">搜索</el-button>
        <!-- 新增 -->
        <div style="display: inline-block;margin: 0px 2px;">
            <el-button
                    class="filter-item"
                    size="mini"
                    type="primary"
                    icon="el-icon-plus"
                    @click="$refs.form.dialog = true">新增</el-button>
            <eForm ref="form"  :is-add="true"/>
        </div>
    </div>
</template>

<script>
    import { parseTime } from '@/utils/index'
    import eForm from './form'
    import DictSelect from '../../../components/DictSelect'
    // 查询条件
    export default {
        components: { eForm ,DictSelect},
        props: {
            query: {
                type: Object,
                required: true
            }
        },
        data() {
            return {
                    billDateAll : '',
                downloadLoading: false
            }
        },
        methods: {
            change_payType(val) {
                this.query.payType = val
            },
            change_status(val) {
                this.query.status = val
            },
            toQuery() {
                this.query.billDate1 = null
                this.query.billDate2 = null
                if (this.billDateAll[0]!= null) { this.query.billDate1 = this.billDateAll[0].toString() }
                if (this.billDateAll[1] != null) { this.query.billDate2 = this.billDateAll[1].toString() }
                this.$parent.page = 0
                this.$parent.init()
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
