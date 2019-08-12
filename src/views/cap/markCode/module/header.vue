<template>
    <div class="head-container">
                            <el-input v-model="query.code" clearable placeholder="输入科目编码" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                    <el-input v-model="query.name" clearable placeholder="输入科目名称" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                    <dict-select v-model="query.status" placeholder="请选择状态" no="Y00006" style="width: 200px;" class="filter-item"  @change="change_status" />
                  <el-date-picker
                    placeholder="请选择创建时间"
                    v-model="createTimeAll"
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
                    createTimeAll : '',
                downloadLoading: false
            }
        },
        methods: {
            change_status(val) {
                this.query.status = val
            },
            toQuery() {
                this.query.createTime1 = null
                this.query.createTime2 = null
                if (this.createTimeAll[0]!= null) { this.query.createTime1 = this.createTimeAll[0].toString() }
                if (this.createTimeAll[1] != null) { this.query.createTime2 = this.createTimeAll[1].toString() }
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
