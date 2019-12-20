<template>
  <div class="binding_add">
    <el-form label-width="110px">
      <el-form-item label="选择设备：">
        <el-select filterable v-model="value" size="small" style="width:300px;" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="选择网关：">
        <el-select filterable v-model="value" size="small" style="width:300px;" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="选择电表：">
        <el-select filterable v-model="value" size="small" style="width:300px;" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
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
      options: [
        {
          value: "选项1",
          label: "黄金糕"
        },
        {
          value: "选项2",
          label: "双皮奶"
        },
        {
          value: "选项3",
          label: "蚵仔煎"
        },
        {
          value: "选项4",
          label: "龙须面"
        },
        {
          value: "选项5",
          label: "北京烤鸭"
        }
      ],
      value: ""
    };
  },
  methods: {},
  created() {}
};
</script>
<style lang="less">
.binding_add {
  padding-top: 12px;
  .el-form-item {
    margin-bottom: 20px;
  }
}
</style>