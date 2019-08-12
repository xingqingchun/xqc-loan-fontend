<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增客户信息' : '编辑客户信息'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="外部业务Id"   prop="outId">
                                <el-input  v-model="form.outId" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="外部编码"   >
                                <el-input v-model="form.outNo" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="编码"   >
                                <el-input  v-model="form.code" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="姓名"  >
                                <el-input v-model="form.custName" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                            <el-form-item  label="类型" >
                                <dict-select v-model="type" placeholder="请选择类型"
                                             no="Y00068" @change="type_change"  style="width: 100%;"/>
                            </el-form-item>
                    </el-col>
                  <el-col   :span="12">
                    <el-form-item label="登陆名"  >
                      <el-input  v-model="form.loginName" style="width: 100%;"   />
                    </el-form-item>

                  </el-col>

                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="手机"  >
                                <el-input  v-model="form.phone" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="邮箱"   >
                                <el-input v-model="form.email" style="width: 100%;"   />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="状态"  >
                                <dict-select v-model="valid" placeholder="请选择状态"
                                             no="Y00067" @change="valid_change"/>
                            </el-form-item>
                    </el-col>
                  <el-col   :span="12">
                    <el-form-item label="上级用户"   >
                      <el-input  v-model="form.upCustId" style="width: 100%;"   />
                    </el-form-item>

                  </el-col>


                </el-row>
                <el-row>

                    <el-col :span="24">
                            <el-form-item label="请求地址"  >
                                <el-input v-model="form.url" style="width: 100%;"   />
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
                form: {id: '' ,outId:'',outNo:'',code:'',custName:'',loginName:'',password:'',address:'',type:'',phone:'',email:'',valid:'',entry:'',upCustId:'',url:''
        },
            rules: {}
        }
        },
        computed: {
            initData() {
                return null
            },
            type() {
                return  String(this.form.type)
            },
            valid() {
                return  String(this.form.valid)
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
            type_change(val) {
                this.form.type = val
            },
            valid_change(val) {
                this.form.valid = val
            },
            cancel() {
                this.resetForm()
            },
            loadData() {
              if(this.id==null||this.id=='')
                return ;
                doGet('/cust/custInfo/getById?id=' + this.id).then(data => {
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
                doPost('/cust/custInfo/save', this.form).then(res => {
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
                doPost('/cust/custInfo/update', this.form).then(res => {
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
                this.form = {id: '' ,outId:'',outNo:'',code:'',custName:'',loginName:'',password:'',address:'',type:'',phone:'',email:'',valid:'',entry:'',upCustId:'',url:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
