<template>
  <div class="gateway_list">
    <div class="top">
      <permission-button permCode banType="hide" size="small" ripple @click="addVisible=true">新增</permission-button>
      <el-button
        ripple
        size="small"
        :disabled="multipleSelection.length!=1"
        @click="editVisible=true"
      >修改</el-button>
      <el-tooltip class="item" effect="light" content="正在开发中..." placement="top-start">
        <el-button ripple size="small" type>导入</el-button>
      </el-tooltip>
      <el-button ripple size="small" :disabled="multipleSelection.length!=1" @click="open">删除</el-button>
      <div class="search">
        关键字：
        <el-input type="search" placeholder="根据设备编号、名称、位号" size="small" v-model="keyword"></el-input>
        <el-button size="small" type @click="getList">搜索</el-button>
      </div>
    </div>
    <div class="bottom">
      <el-table
        @selection-change="handleSelectionChange"
        :data="tableData"
        size="small"
        border
        style="width: 100%"
        :height="500"
      >
        <el-table-column align="center" type="selection" width="50"></el-table-column>
        <el-table-column prop="no" label="网关编码" min-width="160" show-overflow-tooltip></el-table-column>
        <el-table-column prop="name" label="网关名称" min-width="200" show-overflow-tooltip></el-table-column>
        <el-table-column prop="type" label="型号" min-width="180" show-overflow-tooltip></el-table-column>
        <el-table-column prop="transProto" label="传输协议" min-width="100" show-overflow-tooltip>
          <template slot-scope="scope">
            <span>{{scope.row.transProto==1?"TCP":scope.row.transProto==2?"MQTT":scope.row.transProto==3?"CoAP":scope.row.transProto==4?"HTTP/HTTPS":''}}</span>
          </template>
        </el-table-column>
        <el-table-column prop="netType" label="网络类型" min-width="120" show-overflow-tooltip>
          <template slot-scope="scope">
            <span>{{scope.row.netType==1?"WIFI":scope.row.netType==2?"蜂窝(2G/3G/4G/5G)":scope.row.netType==3?"以太网":""}}</span>
          </template>
        </el-table-column>
        <el-table-column prop="addr" label="网关MAC地址" min-width="160" show-overflow-tooltip></el-table-column>
        <el-table-column prop="gmtCreate" label="添加日期" min-width="120" show-overflow-tooltip></el-table-column>
        <el-table-column prop="remark" label="备注" min-width="220" show-overflow-tooltip></el-table-column>
      </el-table>
      <div style="padding:8px 0;text-align:right;">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[10, 20, 30]"
          :page-size="pageSize"
          layout="total, prev, pager, next, sizes,jumper"
          :total="total"
        ></el-pagination>
      </div>
    </div>
    <el-dialog
      title="新增"
      :close-on-click-modal="false"
      :visible.sync="addVisible"
      :destroy-on-close="true"
      width="600px"
      v-if="addVisible"
      :append-to-body="true"
    >
      <add v-on:addDialog="addDialog" ref="addSave"></add>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="addVisible = false">取 消</el-button>
        <el-button size="small" type="primary" @click="tosave">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="修改"
      :close-on-click-modal="false"
      :visible.sync="editVisible"
      :destroy-on-close="true"
      width="600px"
      v-if="editVisible"
      :append-to-body="true"
    >
      <edit
        :id="multipleSelection.length>0?multipleSelection[0].id:null"
        v-on:addDialog="editDialog"
        ref="editSave"
      ></edit>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="editVisible = false">取 消</el-button>
        <el-button size="small" type="primary" @click="toUpdate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import add from "./add";
import edit from "./edit";
export default {
  inject: ["reload"],
  data() {
    return {
      editVisible: false,
      addVisible: false,
      keyword: "",
      tableData: [],
      currentPage: 1,
      pageSize: 10,
      total: 0,
      multipleSelection: []
    };
  },
  methods: {
    open() {
      this.$confirm("确定删除吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.detele();
        })
        .catch(() => {});
    },
    detele() {
      let qs = require("qs");
      let data = qs.stringify({
        gatewayId: this.multipleSelection[0].id
      });
      this.Axios(
        {
          url: "/gateway/del/",
          type: "post",
          params: data,
          option: {
            requestTarget: "p",
            successMsg: "删除成功"
          }
        },
        this
      )
        .then(result => {
          if (result.data.code === 200) {
            console.log(result);
            this.getList();
          }
        })
        .catch(err => {});
    },
    toUpdate() {
      this.$refs.editSave.save();
    },
    tosave() {
      this.$refs.addSave.save();
    },
    addDialog(pamars) {
      this.addVisible = false;
      // this.getList();
      this.reload();
    },
    editDialog(pamars) {
      this.editVisible = false;
      // this.getList();
      this.reload();
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.getList();
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.getList();
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    getList() {
      this.Axios(
        {
          url: "/gateway/getList/",
          type: "get",
          params: {
            page: this.currentPage,
            size: this.pageSize,
            keyword: this.keyword
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
            this.tableData = result.data.data.content;
            this.total = result.data.data.totalElement;
            console.log(result);
            this.multipleSelection = [];
          }
        })
        .catch(err => {});
    }
  },
  created() {
    this.getList();
  },
  components: {
    add,
    edit
  }
};
</script>
<style lang="less">
@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
.gateway_list {
  padding: 10px;
  .top {
    height: 60px;
    line-height: 60px;
    border: 1px solid @Info;
    border-radius: 5px;
    padding-left: 10px;
    .search {
      padding: 0 10px;
      float: right;
      text-align: right;
      width: 60%;
      .el-input {
        width: 300px;
      }
    }
  }
  .bottom {
    padding: 10px;
    font-size: 12px;
    border: 1px solid @Info;
    margin-top: 10px;
    min-height: 500px;
    border-radius: 5px;
  }
}
</style>