<template>
  <div class="gateway_edit">
    <el-form :model="gatewayDTO" ref="gatewayEdit" label-width="110px">
      <el-form-item
        label="网关编码："
        prop="no"
        :rules="[{ required: true, message: '请输入网关编码', trigger: 'blur' }]"
      >
        <el-input size="small" disabled v-model="gatewayDTO.no" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item
        label="网关名称："
        prop="name"
        :rules="[{ required: true, message: '请输入网关名称', trigger: 'blur' }]"
      >
        <el-input size="small" v-model="gatewayDTO.name" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="型号：" prop="type">
        <el-input size="small" v-model="gatewayDTO.type" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item
        label="传输协议："
        prop="transProto"
        :rules="[{ required: true, message: '请选择传输协议', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="gatewayDTO.transProto"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option label="TCP" :value="1"></el-option>
          <el-option label="MQTT" :value="2"></el-option>
          <el-option label="CoAP" :value="3"></el-option>
          <el-option label="HTTP/HTTPS" :value="4"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="端口：" prop="port">
        <el-input
          size="small"
          v-model="gatewayDTO.port"
          style="width:300px;"
          maxlength="20"
          placeholder="1~65535"
        ></el-input>
      </el-form-item>
      <el-form-item
        label="网路类型："
        prop="netType"
        :rules="[{ required: true, message: '请选择网路类型', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="gatewayDTO.netType"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option label="WIFI" :value="1"></el-option>
          <el-option label="蜂窝(2G/3G/4G/5G)" :value="2"></el-option>
          <el-option label="以太网" :value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="网关MAC地址：" prop="addr">
        <el-input
          size="small"
          v-model="gatewayDTO.addr"
          style="width:300px;"
          maxlength="20"
          placeholder=" 如：9822EFF70ED1"
        ></el-input>
      </el-form-item>
      <el-form-item label="备注：" prop="remark">
        <el-input
          v-model="gatewayDTO.remark"
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
      gatewayDTO: {
        addr: "",
        name: "",
        netType: "",
        no: "",
        port: "",
        remark: "",
        transProto: "",
        type: ""
      }
    };
  },
  methods: {
    findOne() {
      this.Axios(
        {
          url: "/gateway/get/",
          type: "get",
          params: { gatewayId: this.id },
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
            this.gatewayDTO = result.data.data;
          }
        })
        .catch(err => {});
    },
    save() {
      this.$refs["gatewayEdit"].validate(valid => {
        if (valid) {
          let qs = require("qs");
          let data = qs.stringify({
            gatewayId: this.id
          });
          this.Axios(
            {
              url: "/gateway/update/",
              type: "post",
              params: this.gatewayDTO,
              option: {
                requestTarget: "p",
                enableMsg: false
              },
              config: {
                headers: { "Content-Type": "application/json" }
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
.gateway_edit {
  padding-top: 12px;
  .el-form-item {
    margin-bottom: 20px;
  }
}
</style>