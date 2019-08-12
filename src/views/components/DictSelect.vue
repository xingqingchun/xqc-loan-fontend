<template>
  <el-select v-model="selectKey" :placeholder="placeholder" style="width: 100%;" @change="change" :disabled="disabled"  :readonly="readonly" >
    <el-option v-for="item in zones" :key="item.id" :label="item.label" :value="String(item.id)"/>
  </el-select>
</template>

<script>
import { doGet } from '@/utils/request'
export default {
  name: 'DictSelect',
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    readonly: {
      type: Boolean,
      default: false
    },
    no: {
      type: String,
      required: true
    },
    initVa: {
      type: String,
      required: false,
      default: ''
    },
    value: {
      type: [String, Number],
      default: ''
    },
    placeholder: {
      type: String,
      default: '请选择类型'
    }
  },
  data() {
    return {
      selectKey: String(this.value),
      zones: []
    }
  },
  watch: {
    value(newValue, oldValue) {
      this.selectKey = String(newValue)
    }
  },
  created() {
    this.getTree()
  },
  methods: {
    getTree() {
      doGet(`/system/dictInfo/queryDictItem?no=${this.no}&va=${this.initVa}`).then(res => {
        this.zones = res
      })
    },
    change(val) {
      this.$emit('change', val)
    }
  }
}
</script>
