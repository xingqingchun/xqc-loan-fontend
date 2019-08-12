<template>
  <el-dialog
    :append-to-body="true"
    :visible.sync="dialog"
    :title="'对账记录明细'"
    width="900px">
    <el-form ref="form" :model="form" :rules="rules" size="small" label-width="85px">
      <el-row>
        <el-col :span="12">
          <el-form-item label="名称" prop="batchName">
            <el-input v-model="form.batchName" />
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="编码" prop="batchCode">
            <dict-select
              v-model="batchCode"
              placeholder="请选择编码"
              no="Y00049"
              @change="batchCode_change"/>
          </el-form-item>
        </el-col>

      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="开始时间" prop="startTime">
            <el-date-picker
              v-model="form.startTime"
              type="date"
              format="yyyy-MM-dd HH:mm:ss"
              value-format="yyyy-MM-dd HH:mm:ss"
              placeholder="选择日期" style="width: 100%;"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="结束时间" prop="endTime">
            <el-date-picker
              v-model="form.endTime"
              type="date"
              format="yyyy-MM-dd HH:mm:ss"
              value-format="yyyy-MM-dd HH:mm:ss"
              placeholder="选择日期" style="width: 100%;"/>
          </el-form-item>
        </el-col>

      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="状态" prop="state">
            <dict-select
              v-model="state"
              placeholder="请选择状态"
              no="Y00048"
              @change="state_change"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="主键" prop="id">
            <el-input v-model="form.id" />
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="错误信息" >
            <el-input v-model="form.errorMsg" rows="2" type="textarea"/>
          </el-form-item>
        </el-col>

      </el-row>
    </el-form>
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
      form: { id: '', batchName: '', batchCode: '', startTime: '', endTime: '', state: '', errorMsg: ''
      },
      rules: {
      }
    }
  },
  computed: {
    initData() {
      return null
    },
    batchCode() {
      return String(this.form.batchCode)
    },
    state() {
      return String(this.form.state)
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
    batchCode_change(val) {
      this.form.batchCode = val
    },
    state_change(val) {
      this.form.state = val
    },
    cancel() {
      this.resetForm()
    },
    loadData() {
      doGet('/adjust/pastRecord/getById?id=' + this.id).then(data => {
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
        if (valid) {
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
    resetForm() {
      this.dialog = false
      this.$refs['form'].resetFields()
      this.form = { id: '', batchName: '', batchCode: '', startTime: '', endTime: '', state: '', errorMsg: ''
      }
    }
  }
}
</script>

<style scoped>

</style>
