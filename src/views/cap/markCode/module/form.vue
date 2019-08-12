<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增资金科目表' : '编辑资金科目表'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="科目编码"   prop="code">
                                <el-input  v-model="form.code" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="科目名称"   prop="name">
                                <el-input v-model="form.name" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="状态"  prop="status">
                                <dict-select v-model="status" placeholder="请选择状态"
                                             no="Y00006" @change="status_change"/>
                            </el-form-item>
                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="备注"   prop="remark">
                                <el-input v-model="form.remark" style="width: 100%;"   />
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
                form: {id: '' ,code:'',name:'',status:'',remark:''
        },
            rules: {
                code:[{required: true, message: '请输入科目编码',trigger: 'blur'}]
                ,name:[{required: true, message: '请输入科目名称',trigger: 'blur'}]
                ,status:[{required: true, message: '请输入状态',trigger: 'blur'}]
                ,remark:[{required: true, message: '请输入备注',trigger: 'blur'}]
            }
        }
        },
        computed: {
            initData() {
                return null
            },
            status() {
                return  String(this.form.status)
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
            status_change(val) {
                this.form.status = val
            },
            cancel() {
                this.resetForm()
            },
            loadData() {
              if(this.id==null||this.id=='')
                return ;
                doGet('/cap/markCode/getById?id=' + this.id).then(data => {
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
                doPost('/cap/markCode/save', this.form).then(res => {
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
                doPost('/cap/markCode/update', this.form).then(res => {
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
                this.form = {id: '' ,code:'',name:'',status:'',remark:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
