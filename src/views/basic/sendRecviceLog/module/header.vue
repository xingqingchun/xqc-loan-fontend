<template>
    <div class="head-container">
                            <el-input v-model="query.code" clearable placeholder="输入业务编码" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                    <el-input v-model="query.bizId" clearable placeholder="输入业务ID" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                  <el-date-picker
                    placeholder="请选择发送时间"
                    v-model="sendTimeAll"
                    class="filter-item"
                    type="daterange"
                    unlink-panels
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"/>
                    <dict-select v-model="query.status" placeholder="请选择状态" no="Y00077" style="width: 200px;" class="filter-item"  @change="change_status" />
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
                    sendTimeAll : '',
                downloadLoading: false
            }
        },
        methods: {
            change_status(val) {
                this.query.status = val
            },
            toQuery() {
                this.query.sendTime1 = null
                this.query.sendTime2 = null
                if (this.sendTimeAll[0]!= null) { this.query.sendTime1 = this.sendTimeAll[0].toString() }
                if (this.sendTimeAll[1] != null) { this.query.sendTime2 = this.sendTimeAll[1].toString() }
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
