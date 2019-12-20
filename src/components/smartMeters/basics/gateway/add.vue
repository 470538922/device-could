<template>
  <div class="gateway_add">
    <el-form label-width="110px">
      <el-form-item label="网关编码：" prop="no">
        <el-input size="small" v-model="gatewayDTO.no" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="网关名称：" prop="name">
        <el-input size="small" v-model="gatewayDTO.name" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="型号：" prop="type">
        <el-input size="small" v-model="gatewayDTO.type" style="width:300px;" maxlength="20"></el-input>
      </el-form-item>
      <el-form-item label="传输协议：" prop="transProto">
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
      <el-form-item label="端口号：" prop="port">
        <el-input
          size="small"
          v-model="gatewayDTO.port"
          style="width:300px;"
          maxlength="20"
          placeholder="1~65535"
        ></el-input>
      </el-form-item>
      <el-form-item label="网路类型：" prop="netType">
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
    getCode() {
      this.Axios(
        {
          url: "/gateway/getNo/",
          type: "get",
          params: {},
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
            this.gatewayDTO.no = result.data.data;
          }
        })
        .catch(err => {});
    },
    save() {
      this.Axios(
        {
          url: "/gateway/add/",
          type: "post",
          params: this.gatewayDTO,
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
    }
  },
  created() {
    this.getCode();
  }
};
</script>
<style lang="less">
.gateway_add {
  padding-top: 12px;
  .el-form-item {
    margin-bottom: 20px;
  }
}
</style>