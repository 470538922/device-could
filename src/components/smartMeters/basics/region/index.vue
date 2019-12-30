<template>
  <div class="organizationManage">
    <div class="content">
      <div class="left">
        <h5 class="componet-name">区域名称</h5>
        <div style="float:right;">
          <!-- <h5 class="classify">类别</h5> -->
          <!-- <h5 class="remarks">备注</h5> -->
        </div>
        <el-tree class="organization-tree" :data="data" default-expand-all :props="defaultProps">
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <span :title="data.name" class="listcontent">
              {{ data.name }}
              <span class="addCase" @click.stop>
                <permission-button
                  permCode
                  banType="disable"
                  type="text"
                  size="mini"
                  @click="toAdd(data)"
                >
                  <el-tooltip class="item" effect="dark" content="添加" placement="top">
                    <i style="font-size:16px" class="iconfont">&#xe62f;</i>
                  </el-tooltip>
                </permission-button>
                <permission-button
                  permCode
                  banType="disable"
                  type="text"
                  size="mini"
                  @click="() => toRevise(data.id)"
                  v-if="data.parentCode!=0"
                >
                  <el-tooltip class="item" effect="dark" content="修改" placement="top">
                    <i style="font-size:16px" class="iconfont">&#xe6b4;</i>
                  </el-tooltip>
                </permission-button>
                <permission-button
                  permCode
                  banType="disable"
                  type="text"
                  size="mini"
                  @click="() => warningdelete(data.id)"
                  v-if="data.parentCode!=0"
                  style="color:#F56C6C"
                >
                  <el-tooltip class="item" effect="dark" content="删除" placement="top">
                    <i style="font-size:16px" class="iconfont">&#xe66b;</i>
                  </el-tooltip>
                </permission-button>
              </span>
            </span>
          </span>
        </el-tree>
      </div>
    </div>
    <el-dialog
      :close-on-click-modal="false"
      title="添加"
      :visible.sync="addShow"
      width="500px"
      v-if="addShow"
    >
      <el-form :model="regionName" label-width="100px" ref="add">
        <el-form-item
          prop="name"
          label="名称："
          style="margin-bottom:20px;padding-top:20px;"
          :rules="[{ required: true, message: '请输入名称', trigger: 'blur' }]"
        >
          <el-input
            type="text"
            size="small"
            v-model="regionName.name"
            style="width:300px;"
            maxlength="20px;"
          ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="addShow = false">取 消</el-button>
        <el-button size="small" type="primary" @click="tosave">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      :close-on-click-modal="false"
      title="修改"
      :visible.sync="reviseShow"
      width="500px"
      v-if="reviseShow"
    >
      <el-form :model="chengedata" label-width="100px" ref="edit">
        <el-form-item
          prop="name"
          label="名称："
          style="margin-bottom:20px;padding-top:20px;"
          :rules="[{ required: true, message: '请输入名称', trigger: 'blur' }]"
        >
          <el-input
            type="text"
            size="small"
            v-model="chengedata.name"
            style="width:300px;"
            maxlength="20px;"
          ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="reviseShow = false">取 消</el-button>
        <el-button size="small" type="primary" @click="toUpdate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
// import add from "./Add";
// import revise from "./Revise";
import { eq } from "semver";

export default {
  inject: ["reload"],
  data() {
    return {
      regionName: { name: "" },
      addShow: false,
      reviseShow: false,
      pushtext: [],
      code: "id",
      data: [],
      defaultProps: {
        children: "children",
        label: "name"
      },
      //当前节点数据
      nodedata: "",
      //xiugai jiedian
      chengedata: "",
      //当前节点ID
      enterpriseId: JSON.parse(localStorage.getItem("user")).enterpriseId
    };
  },
  methods: {
    tosave() {
      this.$refs["add"].validate(valid => {
        if (valid) {
          let qs = require("qs");
          let data = qs.stringify({
            parentCode: this.nodedata.code,
            name: this.regionName.name,
            enterpriseId: this.enterpriseId
          });
          this.Axios(
            {
              url: "/devicePosition/add",
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
                this.regionName.name = "";
                this.addShow = false;
                console.log(result);
                this.$message.success("添加成功");
                this.allOrganize();
              }
            })
            .catch(err => {});
        } else {
          return false;
        }
      });
    },
    toRevise(id) {
      this.reviseShow = true;
      this.findOneOrganize(id);
    },
    toAdd(data) {
      console.log(data);
      this.nodedata = data;
      this.addShow = true;
      return false;
    },
    toUpdate() {
      this.$refs["edit"].validate(valid => {
        if (valid) {
          let qs = require("qs");
          let data = qs.stringify({
            id: this.chengedata.id,
            name: this.chengedata.name
          });
          this.Axios(
            {
              url: "/devicePosition/update",
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
                this.reviseShow = false;
                this.$message.success("修改成功");
                this.allOrganize();
              }
            })
            .catch(err => {});
        } else {
          return false;
        }
      });
    },
    orgdelete(nodeId) {
      //删除组织机构
      let qs = require("qs");
      let data = qs.stringify({
        enterpriseId: this.enterpriseId,
        organizeId: nodeId
      });
      this.Axios(
        {
          url: "/devicePosition/del?id=" + nodeId,
          params: {},
          type: "post",
          option: {
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          if (result.data.code == 200) {
            this.$message.success("删除成功");
            this.allOrganize();
          }
        },
        ({ type, info }) => {}
      );
    },
    allOrganize() {
      this.Axios(
        {
          params: {
            enterpriseId: this.enterpriseId
          },
          type: "get",
          url: "/devicePosition/all",
          option: {
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          if (result.data.code === 200) {
            console.log(result);
            let arr = Math.min.apply(
              null,
              result.data.data.map(item => {
                return item.parentCode;
              })
            );
            this.data = this.filterArray(result.data.data, arr);
            console.log(this.data);
          }
        },
        ({ type, info }) => {}
      );
    },
    findOneOrganize(id) {
      //查询单个组织机构
      this.Axios(
        {
          params: id,
          type: "get",
          url: "/devicePosition/findOne?id=" + id,
          option: {
            enableMsg: false
          }
        },
        this
      )
        .then(result => {
          if (result.data.code === 200) {
            console.log(result);
            this.chengedata = result.data.data;
          }
        })
        .catch(err => {});
    },
    filterArray(data, parent) {
      let vm = this;
      var tree = [];
      var temp;
      for (var i = 0; i < data.length; i++) {
        if (data[i].parentCode == parent) {
          var obj = data[i];
          temp = this.filterArray(data, data[i].code);
          if (temp.length > 0) {
            obj.children = temp;
          }
          tree.push(obj);
        }
      }
      return tree;
    },
    warningdelete(nodeId) {
      this.$confirm("确定删除吗？", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.orgdelete(nodeId);
        })
        .catch(() => {});
    }
  },
  mounted() {
    let li = document.querySelectorAll(".left li");
    for (let i = 0; i < li.length; i++) {
      li[i].onmouseover = function(event) {
        document.querySelectorAll(".content li span")[i].style.display =
          "inline-block";
        event.stopPropagation();
      };
      li[i].onmouseout = function(event) {
        document.querySelectorAll(".content li span")[i].style.display = "none";
        event.stopPropagation();
      };
    }
    let a = JSON.parse(localStorage.getItem("user"));
  },
  created() {
    this.allOrganize();
  },
  components: {
    // revise,
    // add
  }
};
</script>
<style lang="less">
// @import url("../../assets/font/font.css");

@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
@border: 1px solid #dde2eb;

.organizationManage {
  // padding-left: 180px;
  font-size: 12px;
  .el-tree-node__content {
    height: 40px;
    border-top: solid 1px #eee;
  }
  add {
    position: absolute;
    top: 50%;
    left: 50%;
  }
  .content {
    overflow: auto;
    padding: 10px;
    border: @border;
    border-radius: 5px;
    //max-height: 500px;
    .left {
      width: 99%;
      // padding: 10px;
      overflow: hidden;
      //float: left;
      display: inline-block;
      margin-left: 5px;
      // text-align: center;
      // border: @border;

      .componet-name {
        line-height: 30px;
        width: 500px;
        // border: @border;
        padding-left: 30px;
        float: left;
      }
      .remarks {
        width: 400px;
        float: left;
        // border: @border;
        line-height: 30px;
        text-align: left;
      }
      .classify {
        padding-right: 10px;
        width: 100px;
        // border: @border;
        float: left;
        line-height: 30px;
        text-align: center;
      }
    }
  }
  .organization-tree {
    width: 100%;
    overflow: hidden;
    .custom-tree-node {
      width: 100%;
      text-align: right;
      // height: 40px;
      .listcontent {
        // display: inline-block;
        float: left;
        text-align: left;
        width: 400px;
        position: relative;
        .addCase {
          position: relative;
          display: inline-block;
          margin-left: 30px;
          opacity: 0;
          z-index: -10;
        }
        &:hover {
          .addCase {
            opacity: 1;
            z-index: 10;
          }
        }
      }
      .state {
        display: inline-block;
        width: 100px;
        line-height: 28px;
        text-align: center;
        // border: @border;
      }
      .organizeInfo {
        display: inline-block;
        text-align: left;
        width: 400px;
        line-height: 28px;
        // border: @border;
      }

      .state {
      }
    }
  }
}
</style>
