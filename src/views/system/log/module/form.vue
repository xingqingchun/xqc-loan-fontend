<template>
  <el-dialog :append-to-body="true" :visible.sync="dialog" :title=" '操作日志'"
             width="900px">
    <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
      <el-row>
        <el-col :span="12">
          <el-form-item label="系统类别" prop="sysType">
            <dict-select v-model="sysType" placeholder="请选择系统类别"
                         no="Y00078" @change="sysType_change" :readonly="true"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="日子编码" prop="code">
            <el-input v-model="form.code" style="width: 100%;" :readonly="true"/>
          </el-form-item>

        </el-col>

        <el-row>
          <el-col :span="12">
            <el-form-item label="日志名称" prop="name">
              <el-input v-model="form.name" style="width: 100%;" :readonly="true"/>
            </el-form-item>

          </el-col>
          <el-col :span="12">
            <el-form-item label="请求ip" prop="requestIp">
              <el-input v-model="form.requestIp" style="width: 100%;" :readonly="true"/>
            </el-form-item>
          </el-col>

        </el-row>
      </el-row>
      <el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="参数" prop="params">
              <el-input v-model="form.params" style="width: 100%;" type="textarea" :row="7" :readonly="true"/>
            </el-form-item>

          </el-col>
        </el-row>
        <el-col :span="24">
          <el-form-item label="异常详情" prop="exceptionMsg">
            <el-input v-model="form.exceptionMsg" style="width: 100%;" type="textarea" :row="7" :readonly="true"/>
          </el-form-item>

        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="状态" prop="status">
            <dict-select v-model="status" placeholder="请选择状态"
                         no="Y00079" @change="status_change" :readonly="true"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="业务ID" prop="bizId">
            <el-input v-model="form.bizId" style="width: 100%;" :readonly="true"/>
          </el-form-item>

        </el-col>


      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="日志名称" prop="name">
            <el-input v-model="form.name" style="width: 100%;":readonly="true"/>
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="请求ip" prop="requestIp">
            <el-input v-model="form.requestIp" style="width: 100%;":readonly="true"/>
          </el-form-item>
        </el-col>

      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="耗时" prop="time">
            <el-input v-model="form.time" style="width: 100%;":readonly="true"/>
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="操作用户" prop="userName">
            <el-input v-model="form.userName" style="width: 100%;":readonly="true"/>
          </el-form-item>

        </el-col>


      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="用户ID" prop="userId">
            <el-input v-model="form.userId" style="width: 100%;":readonly="true"/>
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
  import { doPost, doGet } from '@/utils/request'
  import DictSelect from '../../../components/DictSelect'

  export default {
    components: { DictSelect },
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
        form: {
          id: '',
          sysType: '',
          code: '',
          name: '',
          exceptionMsg: '',
          status: '',
          bizId: '',
          params: '',
          requestIp: '',
          time: '',
          userName: '',
          userId: ''
        },
        rules: {

        }
      }
    },
    computed: {
      initData() {
        return null
      },
      sysType() {
        return String(this.form.sysType)
      },
      status() {
        return String(this.form.status)
      }
    },
    watch: {
      dialog(newVal) {
        if (newVal) {
          this.loadData()
        }
      }
    },
    methods: {
      sysType_change(val) {
        this.form.sysType = val
      },
      status_change(val) {
        this.form.status = val
      },
      cancel() {
        this.resetForm()
      },
      loadData() {
        if (this.id == null || this.id == '') {
          return
        }
        doGet('/system/log/getById?id=' + this.id).then(data => {
            this.loading = false
            this.form = data
          }
        ).catch(err => {
            this.delLoading = false
            this.$refs[id].doClose()
            let msg = '系统异常'
            if (err.code != undefined && err.code != null) {
              msg = err.msg
            } else if (err.response.data != undefined && err.response.data != null) {
              msg = err.response.data.msg
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
        this.form = {
          id: '',
          sysType: '',
          code: '',
          name: '',
          exceptionMsg: '',
          status: '',
          bizId: '',
          params: '',
          requestIp: '',
          time: '',
          userName: '',
          userId: ''
        }
      }
    }
  }
</script>

<style scoped>

</style>
