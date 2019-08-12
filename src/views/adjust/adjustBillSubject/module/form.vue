<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增账单科目' : '编辑账单科目'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="科目名称"   prop="name">
                                <el-input  v-model="form.name" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="科目编码"   prop="code">
                                <el-input v-model="form.code" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    
                    
                </el-row>
                <el-row>
                    <el-col   :span="24">
                            <el-form-item label="状态"  prop="status">
                                <dict-select v-model="status" placeholder="请选择状态"
                                             no="Y002" @change="status_change"/>
                            </el-form-item>
                    </el-col>
                    
                </el-row>
                <el-row>
                    <el-col   :span="24">
                            <el-form-item  label="备注"  prop="remark">
                                <el-input v-model="form.remark" style="width: 100%;" rows="2" type="textarea"/>
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
                form: {id: '' ,name:'',code:'',status:'',remark:''
        },
            rules: {
                name:[{required: true, message: '请输入科目名称',trigger: 'blur'}]
                ,code:[{required: true, message: '请输入科目编码',trigger: 'blur'}]
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
            if(this.id==null||this.id==''){
                return ;
               }
                doGet('/adjust/adjustBillSubject/getById?id=' + this.id).then(data => {
                        this.loading = false
                        this.form = data
                    }
                ).catch(err => {
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
                doPost('/adjust/adjustBillSubject/save', this.form).then(res => {
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
                }
            )
            },
            doEdit() {
                doPost('/adjust/adjustBillSubject/update', this.form).then(res => {
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
                }
            )
            },
            resetForm() {
                this.dialog = false
                this.$refs['form'].resetFields()
                this.form = {id: '' ,name:'',code:'',status:'',remark:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
