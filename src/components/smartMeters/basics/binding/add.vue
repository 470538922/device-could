<template>
  <div class="binding_add">
    <el-form :model="deviceAndMeterDTO" ref="bindingAdd" label-width="110px">
      <el-form-item
        label="选择设备："
        prop="realDeviceId"
        :rules="[{ required: true, message: '请选择设备', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="deviceAndMeterDTO.realDeviceId"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option
            v-for="item in equipmentList"
            :key="item.id"
            :label="item.label"
            :value="item.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item
        label="选择网关："
        prop="gatewayId"
        :rules="[{ required: true, message: '请选择网关', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="deviceAndMeterDTO.gatewayId"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option
            v-for="item in gatewayList"
            :key="item.id"
            :label="item.label"
            :value="item.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item
        label="选择电表："
        prop="meterId"
        :rules="[{ required: true, message: '请选择电表', trigger: 'blur' }]"
      >
        <el-select
          filterable
          v-model="deviceAndMeterDTO.meterId"
          size="small"
          style="width:300px;"
          placeholder="请选择"
        >
          <el-option
            v-for="item in ammeterList"
            :key="item.id"
            :label="item.label"
            :value="item.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="备注：" prop="remark">
        <el-input
          v-model="deviceAndMeterDTO.remark"
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
      equipmentList: [],
      ammeterList: [],
      gatewayList: [],
      deviceAndMeterDTO: {
        gatewayId: "",
        meterId: "",
        realDeviceId: "",
        remark: ""
      }
    };
  },
  methods: {
    save() {
      // this.deviceAndMeterDTO.realDeviceId = String(
      //   this.deviceAndMeterDTO.realDeviceId
      // );
      this.$refs["bindingAdd"].validate(valid => {
        if (valid) {
          this.Axios(
            {
              url: "/bind",
              type: "post",
              params: this.deviceAndMeterDTO,
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
    },
    equipment() {
      this.Axios(
        {
          params: {
            page: -1,
            size: 10
          },
          option: {
            enableMsg: false
          },
          type: "get",
          url: "/device/all"
        },
        this
      ).then(
        result => {
          if (result.data.code === 200) {
            this.equipmentList = result.data.data.map(item => {
              return {
                ...item,
                label: item.deviceNo + "--" + item.deviceName
              };
            });
          }
        },
        ({ type, info }) => {
          //错误类型 type=faild / error
          //error && error(type, info);
        }
      );
    },
    gateway() {
      this.Axios(
        {
          url: "/gateway/getList",
          type: "get",
          params: {
            page: 1,
            size: -1
          },
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
            this.gatewayList = result.data.data.map(item => {
              return {
                ...item,
                label: item.no + "--" + item.name + "--" + item.type
              };
            });
          }
        })
        .catch(err => {});
    },
    meter() {
      this.Axios(
        {
          url: "/meter/getAvailable",
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
            this.ammeterList = result.data.data.map(item => {
              return {
                ...item,
                label: item.no + "--" + item.name + "--" + item.meterType
              };
            });
          }
        })
        .catch(err => {});
    }
  },
  created() {
    this.equipment();
    this.meter();
    this.gateway();
  }
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