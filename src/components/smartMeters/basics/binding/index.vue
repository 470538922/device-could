<template>
  <div class="binding_list">
    <div class="top">
      <permission-button permCode banType="hide" size="small" ripple @click="addVisible=true">新增</permission-button>
      <el-button ripple size="small" type>解绑</el-button>
      <div class="search">
        关键字：
        <el-input type="search" placeholder="根据设备编号、名称、位号" size="small" v-model="keyword"></el-input>
        <el-button size="small" type>搜索</el-button>
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
        <el-table-column prop="date" label="设备编号" min-width="180" show-overflow-tooltip></el-table-column>
        <el-table-column prop="name" label="设备名称" min-width="300" show-overflow-tooltip></el-table-column>
        <el-table-column prop="address" label="网关" min-width="300" show-overflow-tooltip></el-table-column>
        <el-table-column prop="date" label="电表" min-width="300" show-overflow-tooltip></el-table-column>
        <el-table-column prop="name" label="添加日期" min-width="110" show-overflow-tooltip></el-table-column>
        <el-table-column prop="address" label="备注" min-width="320" show-overflow-tooltip></el-table-column>
      </el-table>
      <div style="padding:8px 0;">
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
      :destroy-on-close="true"
      :visible.sync="addVisible"
      width="600px"
    >
      <add></add>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="addVisible = false">取 消</el-button>
        <el-button size="small" type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import add from "./add";
export default {
  data() {
    return {
      addVisible: false,
      keyword: "",
      tableData: [
        {
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄"
        },
        {
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄"
        }
      ],
      currentPage: 1,
      pageSize: 10,
      total: 100,
      multipleSelection: []
    };
  },
  methods: {
    handleSizeChange(val) {
      this.pageSize = val;
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      console.log(`当前页: ${val}`);
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
      console.log(val);
    }
  },
  created() {},
  components: {
    add
  }
};
</script>
<style lang="less">
@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
.binding_list {
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