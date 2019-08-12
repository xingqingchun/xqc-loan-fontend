<template>
  <el-dialog
    :append-to-body="true"
    :visible.sync="dialog"
    :title="'对账记录明细'"
    width="900px">
    <el-form ref="form" :model="form" :rules="rules" size="small" label-width="100px">
      <el-row>
        <el-col :span="12">
          <el-form-item label="编码" prop="subjectCode">
            <el-input v-model="form.subName" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="记录ID" prop="custId">
            <el-input v-model="form.recordId" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="姓名" prop="custId">
            <el-input v-model="form.custName" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="账户" prop="accountId">
            <el-input v-model="form.accountId" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="初始总金额" prop="totalInitAmount" >
            <el-input v-model="form.totalInitAmount" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="增加总金额" prop="totalAddAmount">
            <el-input v-model="form.totalAddAmount" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="最终总金额" prop="totalLastAmount">
            <el-input v-model="form.totalLastAmount" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="初始冻结金额" prop="frozenInitAmount">
            <el-input v-model="form.frozenInitAmount" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="增加冻结金额" prop="frozenAddAmount">
            <el-input v-model="form.frozenAddAmount" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="最终冻结金额" prop="frozenLastAmount">
            <el-input v-model="form.frozenLastAmount" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="初始可用金额" prop="balanceInitAmount">
            <el-input v-model="form.balanceInitAmount" />
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="新增可用金额" prop="balanceAddAmount">
            <el-input v-model="form.balanceAddAmount" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="最终可用金额" prop="balanceLastAmount">
            <el-input v-model="form.balanceLastAmount" />
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="开始时间" prop="startTime">
            <el-date-picker
              v-model="form.startTime"
              type="date"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
              placeholder="选择日期" style="width: 100%;" />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="结束时间" prop="endTime">
            <el-date-picker
              v-model="form.endTime"
              type="date"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
              placeholder="选择日期" style="width: 100%;" />
          </el-form-item>

        </el-col>
        <el-col :span="12">
          <el-form-item label="状态" prop="status">
            <dict-select
              v-model="status"
              placeholder="请选择状态"
              no="Y00048"/>
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item label="错误" prop="errorMsg">
        <el-input v-model="form.errorMsg" />
      </el-form-item>

      <el-form-item label="备注" prop="remark">
        <el-input v-model="form.remark" />
      </el-form-item>

    </el-form>
  </el-dialog>
</template>

<script>
import DictSelect from '../../../components/DictSelect'
import { doGet } from '../../../../utils/request'

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
      loading: false,
      dialog: false,
      form: { id: '', subjectCode: '', recordId: '', custId: '', accountId: '', totalInitAmount: '', totalAddAmount: '', totalLastAmount: '', frozenInitAmount: '', frozenAddAmount: '', frozenLastAmount: '', balanceInitAmount: '', balanceAddAmount: '', balanceLastAmount: '', startTime: '', endTime: '', errorMsg: '', status: '', remark: ''
      },
      rules: {}
    }
  },
  computed: {
    status() {
      return this.form && (this.form.status ? String(this.form.status) : '')
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
    loadData() {
      if(this.id==null||this.id=='')
        return ;
      doGet('/adjust/pastAccount/getById?id=' + this.id).then(data => {
        this.loading = false
        this.form = data
      }
      ).catch(err => {
        this.loading = false
        console.log(err.msg)
      }
      )
    },
    cancel() {
      this.resetForm()
    }
  }
}
</script>

<style scoped>

</style>
