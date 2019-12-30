<template>
  <div class="add" v-show="show">
    <div class="addcase">
      <el-form label-width="100px">
        <el-form-item label="名称：">
          <el-input type="text" size="small" v-model="orgname" style="width:273px;"></el-input>
        </el-form-item>
      </el-form>
      <div style="text-align:right;margin-top:20px">
        <el-button size="small" @click="addHide">取消</el-button>
        <el-button size="small" @click="beforeAdd" type="primary">保存</el-button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  inject: ["reload"],
  name: "",
  props: {
    nodedata: { default: null }
  },
  data() {
    return {
      show: true,
      type: "",
      value: "",
      orgname: "",
      orgInfo: "",
      nodeMsg: {
        name: ""
      }
    };
  },
  created: function() {
    this.optionSet();
  },
  watch: {
    nodedata() {
      this.optionSet();
    },
    addShow() {
      if (this.addShow === false) {
        this.orgname = "";
        this.value = "";
        this.orgInfo = "";
      }
    }
  },
  methods: {
    append(data) {
      const newChild = { id: id++, label: "text", children: [] };
      if (!data.children) {
        this.$set(data, "children", []);
      }
      data.children.push(newChild);
    },
    handleChange(value) {},
    addHide() {
      this.$emit("addHide", false);
    },
    add() {
      //添加组织机构
      let qs = require("qs");
      let data = qs.stringify({
        parentCode: this.nodedata.code,
        name: this.orgname,
        organizeType: this.value,
        organizeInfo: this.orgInfo
      });
      this.Axios(
        {
          url: "/organize/add",
          params: data,
          type: "post",
          option: {
            enableMsg: false
          }
        },
        this
      )
        .then(result => {
          if (result.data.code === 200) {
            this.orgname = "";
            this.value = "";
            this.orgInfo = "";
            this.$message.success("添加成功");
            this.reload();
          } else {
            this.$message("添加失败");
          }
        })
        .catch(err => {});
    },
    beforeAdd() {
      if (this.orgname === "" || this.value === "") {
        this.$message.warning("请完整填写信息");
      } else {
        this.add();
      }
    }
  },
  created() {}
};
</script>
<style lang="less" scoped>
@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
@border: 1px solid #dde2eb;
.add {
  // width: 100vw;
  // height: 100vh;

  // background-color: rgba(0, 0, 0, 0.103);
  // position: absolute;
  // top: 0%;
  // left: 0%;
  //   margin-left: -150px;
  //   margin-top: -150px;
  .addcase {
    width: 500px;

    // position: relative;
    // top: 50%;
    // left: 50%;
    // margin-top: -150px;
    // margin-left: -150px;
    // background-color: white;
    padding: 15px;
    // border-radius: 5px;
    // box-shadow: 4px 5px 10px @Info;
    li {
      margin-top: 20px;
      text-align: right;
      list-style-type: none;
      width: 100%;
      padding-right: 50px;
      textarea {
        resize: none;
        border-radius: 5px;
        border-color: #dde2eb;
        padding: 10px;
      }
      .el-input {
        width: 70%;
      }
    }
  }
}
</style>
