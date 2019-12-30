<template>
  <div class="ammeter_add">
    <el-form :model="electricMeterDTO" label-width="110px" ref="ammeterEdit">
      <el-form-item
        label="电表编码："
        prop="no"
        :rules="[{ required: true, message: '请输入电表编码', trigger: 'blur' }]"
      >
        <el-input
          size="small"
          disabled
          v-model="electricMeterDTO.no"
          style="width:300px;"
          maxlength="20"
        ></el-input>
      </el-form-item>
      <el-form-item
        label="电表名称："
        prop="name"
        :rules="[{ required: true, message: '请输入电表名称', trigger: 'blur' }]"
      >
        <el-input size="small" v-model="electricMeterDTO.name" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="型号：" prop="meterType">
        <el-input
          size="small"
          v-model="electricMeterDTO.meterType"
          style="width:300px;"
          maxlength="20"
        ></el-input>
      </el-form-item>
      <el-form-item
        label="通信协议："
        prop="commProto"
        :rules="[{ required: true, message: '请选择通信协议', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="electricMeterDTO.commProto"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option label="Modbus" :value="1"></el-option>
          <el-option label="DLT-645-2007" :value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item
        label="电表地址："
        prop="meterAddr"
        :rules="[{ required: true, message: '请输入电表地址', trigger: 'blur' }]"
      >
        <el-input
          size="small"
          v-model="electricMeterDTO.meterAddr"
          style="width:300px;"
          maxlength="20"
          placeholder=" 如：9822EFF70ED1"
        ></el-input>
      </el-form-item>
      <el-form-item label="备注：" prop="remark">
        <el-input
          v-model="electricMeterDTO.remark"
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
  props: {
    id: { default: null }
  },
  data() {
    return {
      value: "",
      electricMeterDTO: {
        commProto: null,
        meterAddr: "",
        meterType: "",
        name: "",
        no: "",
        remark: ""
      }
    };
  },
  methods: {
    findOne() {
      this.Axios(
        {
          url: "/meter/get",
          type: "get",
          params: { meterId: this.id },
          option: {
            requestTarget: "p",
            enableMsg: false
          }
        },
        this
      )
        .then(result => {
          if (result.data.code === 200) {
            console.log(result);
            this.electricMeterDTO = result.data.data;
          }
        })
        .catch(err => {});
    },
    save() {
      this.$refs["ammeterEdit"].validate(valid => {
        if (valid) {
          this.Axios(
            {
              url: "/meter/update",
              type: "post",
              params: this.electricMeterDTO,
              option: {
                requestTarget: "p",
                enableMsg: false
              }
            },
            this
          )
            .then(result => {
              if (result.data.code === 200) {
                console.log(result);
                this.$emit("addDialog", false);
              }
            })
            .catch(err => {});
        } else {
          return false;
        }
      });
    }
  },
  created() {
    this.findOne();
  }
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