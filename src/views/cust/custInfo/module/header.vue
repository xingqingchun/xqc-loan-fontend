<template>
    <div class="head-container">

      <el-input v-model="query.code" clearable placeholder="输入编码" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
      <el-input v-model="query.custName" clearable placeholder="输入姓名" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
      <el-input v-model="query.outId" clearable placeholder="输入外部业务Id" style="width: 200px;"     class="filter-item" @keyup.enter.native="toQuery"/>
                    <dict-select v-model="query.type" placeholder="请选择类型" no="Y00068" style="width: 200px;" class="filter-item"  @change="change_type" />
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
                downloadLoading: false
            }
        },
        methods: {
            change_type(val) {
                this.query.type = val
            },
            toQuery() {
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
