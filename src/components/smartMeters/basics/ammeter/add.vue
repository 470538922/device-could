<template>
  <div class="ammeter_add">
    <el-form label-width="110px">
      <el-form-item label="电表编码：">
        <el-input size="small" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="电表名称：">
        <el-input size="small" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="型号：">
        <el-input size="small" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="通信协议：">
        <el-select filterable v-model="value" size="small" style="width:300px;" placeholder="请选择">
          <el-option label="Modbus" value="Modbus"></el-option>
          <el-option label="DLT-645-2007" value="DLT-645-2007"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="电表地址：">
        <el-input size="small" style="width:300px;" maxlength="20" placeholder=" 如：9822EFF70ED1"></el-input>
      </el-form-item>
      <el-form-item label="备注：">
        <el-input
          type="textarea"
          resize="none"
          :autosize="{ minRows: 4, maxRows: 4}"
          style="width:400px;"
        ></el-input>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    return {
      value: ""
    };
  },
  methods: {},
  created() {}
};
</script>
<style lang="less">
.ammeter_add {
  padding-top: 12px;
  .el-form-item {
    margin-bottom: 20px;
  }
}
</style>