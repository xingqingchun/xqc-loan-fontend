<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增对账文件' : '编辑对账文件'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="支付类型"  prop="payType">
                                <dict-select v-model="payType" placeholder="请选择支付类型"
                                             no="Y01" @change="payType_change"/>
                            </el-form-item>
                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="渠道ID"   prop="channelId">
                                <el-input v-model="form.channelId" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    
                    
                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="渠道编码"   prop="channelCode">
                                <el-input  v-model="form.channelCode" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="文件路径ID"   prop="fileId">
                                <el-input v-model="form.fileId" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    
                    
                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="状态 0未对账 1已对账 9对账不平"  prop="status">
                                <dict-select v-model="status" placeholder="请选择状态 0未对账 1已对账 9对账不平"
                                             no="Y02" @change="status_change"/>
                            </el-form-item>
                    </el-col>
                    <el-col :span="12">
                            <el-form-item  label="账单日期"  prop="billDate">
                                <el-date-picker
                                        v-model="form.billDate"
                                        type="date"
                                        format="yyyy-MM-dd"
                                        value-format="yyyy-MM-dd"
                                        placeholder="选择日期" style="width: 100%;"/>
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
                form: {id: '' ,payType:'',channelId:'',channelCode:'',fileId:'',status:'',billDate:''
        },
            rules: {
                payType:[{required: true, message: '请输入支付类型',trigger: 'blur'}]
                ,channelId:[{required: true, message: '请输入渠道ID',trigger: 'blur'}]
                ,channelCode:[{required: true, message: '请输入渠道编码',trigger: 'blur'}]
                ,fileId:[{required: true, message: '请输入文件路径ID',trigger: 'blur'}]
                ,status:[{required: true, message: '请输入状态 0未对账 1已对账 9对账不平',trigger: 'blur'}]
                ,billDate:[{required: true, message: '请输入账单日期',trigger: 'blur'}]
            }
        }
        },
        computed: {
            initData() {
                return null
            },
            payType() {
                return  String(this.form.payType)
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
            payType_change(val) {
                this.form.payType = val
            },
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
                doGet('/adjust/adjustBillFile/getById?id=' + this.id).then(data => {
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
                doPost('/adjust/adjustBillFile/save', this.form).then(res => {
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
                doPost('/adjust/adjustBillFile/update', this.form).then(res => {
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
                this.form = {id: '' ,payType:'',channelId:'',channelCode:'',fileId:'',status:'',billDate:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
