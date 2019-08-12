<template>
    <el-dialog :append-to-body="true" :visible.sync="dialog" :title="isAdd ? '新增接口发送日志表' : '编辑接口发送日志表'"
               width="900px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px"  >
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="业务编码" >
                                <el-input  v-model="form.code" style="width: 100%;"   :readonly="true"/>
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="业务ID"   >
                                <el-input v-model="form.bizId" style="width: 100%;"   :readonly="true" />
                            </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item  label="发送时间"  >
                                <el-date-picker
                                        v-model="form.sendTime"
                                        type="date"
                                        format="yyyy-MM-dd"
                                        value-format="yyyy-MM-dd"
                                        placeholder="选择日期" style="width: 100%;"  :readonly="true"/>
                            </el-form-item>
                    </el-col>
                  <el-col   :span="12">
                    <el-form-item label="返回时间"   >
                      <el-input  v-model="form.retTime" style="width: 100%;"   :readonly="true" />
                    </el-form-item>

                  </el-col>

                </el-row>

          <el-row>

            <el-col :span="24">
              <el-form-item label="发送数据"   >
                <el-input v-model="form.sendData" style="width: 100%;"  type="textarea"  :rows="7"  :readonly="true"/>
              </el-form-item>
            </el-col>

          </el-row>
                <el-row>

                    <el-col :span="24">
                            <el-form-item label="返回数据"   >
                                <el-input v-model="form.retData" style="width: 100%;"  type="textarea"  :rows="7"  :readonly="true" />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="12">
                            <el-form-item label="返回编码"   >
                                <el-input  v-model="form.retCode" style="width: 100%;"  :readonly="true"  />
                            </el-form-item>

                    </el-col>
                    <el-col :span="12">
                            <el-form-item label="返回信息"   >
                                <el-input v-model="form.retMsg" style="width: 100%;"   :readonly="true" />
                            </el-form-item>

                    </el-col>


                </el-row>
                <el-row>
                    <el-col   :span="24">
                            <el-form-item label="状态"  prop="status">
                                <dict-select v-model="status" placeholder="请选择状态"
                                             no="Y00077" @change="status_change"  :readonly="true"/>
                            </el-form-item>
                    </el-col>

                </el-row>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button type="text" @click="cancel">关闭</el-button>
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
                form: {id: '' ,code:'',bizId:'',sendTime:'',sendData:'',retTime:'',retData:'',retCode:'',retMsg:'',status:''
        },
            rules: {
                code:[{required: true, message: '请输入业务编码',trigger: 'blur'}]
                ,bizId:[{required: true, message: '请输入业务ID',trigger: 'blur'}]
                ,sendTime:[{required: true, message: '请输入发送时间',trigger: 'blur'}]
                ,sendData:[{required: true, message: '请输入发送数据',trigger: 'blur'}]
                ,retTime:[{required: true, message: '请输入返回时间',trigger: 'blur'}]
                ,retData:[{required: true, message: '请输入返回数据',trigger: 'blur'}]
                ,retCode:[{required: true, message: '请输入返回编码',trigger: 'blur'}]
                ,retMsg:[{required: true, message: '请输入返回信息',trigger: 'blur'}]
                ,status:[{required: true, message: '请输入状态',trigger: 'blur'}]
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
                doGet('/basic/sendRecviceLog/getById?id=' + this.id).then(data => {
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
                doPost('/basic/sendRecviceLog/save', this.form).then(res => {
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
                doPost('/basic/sendRecviceLog/update', this.form).then(res => {
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
                this.form = {id: '' ,code:'',bizId:'',sendTime:'',sendData:'',retTime:'',retData:'',retCode:'',retMsg:'',status:''
            }
            }
        }
    }
</script>

<style scoped>

</style>
