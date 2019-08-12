<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增账户资金标记明细' : '编辑账户资金标记明细'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="账户ID"  >
                                <el-input  v-model="form.accountId" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="客户ID"  >
                                <el-input v-model="form.custId" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="科目编码"  >
                                <el-input  v-model="form.capCode" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="科目名称"  >
                                <el-input v-model="form.capName" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="总金额"  >
                                <el-input  v-model="form.toalAmt" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="冻结金额"  >
                                <el-input v-model="form.frozenAmt" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="24">
                            <el-form-item label="可用金额"  >
                                <el-input  v-model="form.balanceAmount" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>

                </el-row>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button type="text" @click="cancel">取消</el-button>
            <el-button :loading="loading" type="primary" @click="doSubmit">确认</el-button>
        </div>
    </el-dialog>
</template>

<script>
    import {doPost,doGet} from '@/utils/request'
    import DictSelect from '../../../components/DictSelect'

    export default {
        components: {DictSelect},
        props: {
            id: {
                type: String,
                default: ''
            },
            isAdd: {
                type: Boolean,
                required: true
            },
            sup_this: {
                type: Object,
                default: null
            }
        },
        data() {
            return {
                loading: false, dialog: false,
                form: {id: '' ,accountId:'',custId:'',capCode:'',capName:'',toalAmt:'',frozenAmt:'',balanceAmount:''
        },
            rules: {
                accountId:[]
                ,custId:[]
                ,capCode:[]
                ,capName:[]
                ,toalAmt:[]
                ,frozenAmt:[]
                ,balanceAmount:[]
            }
        }
        },
        computed: {
            initData() {
                return null
            },
        },
        watch: {
            dialog(newVal) {
                if (newVal) {
                    this.loadData()
                }
            }
        },
        methods: {
            cancel() {
                this.resetForm()
            },
            loadData() {
              if(this.id==null||this.id=='')
                return ;
                doGet('/cap/markDetail/getById?id=' + this.id).then(data => {
                        this.loading = false
                        this.form = data
                    }
                ).catch(err => {
                        this.loading = false
                        console.log(err.msg)
                    }
                )
            },
            doSubmit() {
                this.$refs['form'].validate((valid) => {
                    if(valid) {
                        this.loading = true
                        if (this.isAdd) {
                            this.doAdd()
                        } else this.doEdit()
                    } else {
                        return false
                    }
                }
            )
            },
            doAdd() {
                doPost('/cap/markDetail/save', this.form).then(res => {
                    this.resetForm()
                    this.$notify({
                        title: '添加成功',
                        type: 'success',
                        duration: 2500
                    })
                    this.loading = false
                    this.$parent.$parent.init()
                }
            ).
                catch(err => {
                    this.loading = false
                    console.log(err.msg)
                }
            )
            },
            doEdit() {
                doPost('/cap/markDetail/update', this.form).then(res => {
                    this.resetForm()
                    this.$notify({
                        title: '修改成功',
                        type: 'success',
                        duration: 2500
                    })
                    this.loading = false
                    this.sup_this.init()
                }
            ).
                catch(err => {
                    this.loading = false
                    console.log(err.msg)
                }
            )
            },
            resetForm() {
                this.dialog = false
                this.$refs['form'].resetFields()
                this.form = {id: '' ,accountId:'',custId:'',capCode:'',capName:'',toalAmt:'',frozenAmt:'',balanceAmount:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
