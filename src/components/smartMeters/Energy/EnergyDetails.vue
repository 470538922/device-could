<template>
  <div class="energy_details">
    <el-col :span="4">
      <div class="left_case">
        <el-collapse v-model="activeNames" @change="collapseItemChange">
          <el-collapse-item title="所有区域" name="1">
            <div style>
              <el-tree
                ref="tree"
                :data="regionTreeData"
                highlight-current
                default-expand-all
                node-key="id"
                :expand-on-click-node="false"
                @node-click="treeNodeClick"
                :props="defaultProps"
              ></el-tree>
            </div>
          </el-collapse-item>
          <el-collapse-item title="所有类别" name="2">
            <div style>
              <el-tree
                ref="tree2"
                :data="equTypeTreeData"
                highlight-current
                default-expand-all
                node-key="id"
                :expand-on-click-node="false"
                @node-click="classifyNodeclick"
                :props="defaultProps1"
              ></el-tree>
            </div>
          </el-collapse-item>
        </el-collapse>
      </div>
    </el-col>
    <el-col :span="20" style="padding-left:10px;">
      <el-col :span="10" style="padding-right:10px;position: relative;">
        <el-button-group class="btn_right">
          <el-button
            type
            size="mini"
            :style="{'background-color':leftBtnIndex==1?'#409eff':'','color':leftBtnIndex==1?'#ecf5ff':'','border-color':leftBtnIndex==1?'#409eff':''}"
            @click="changeClickLeft(1)"
          >周</el-button>
          <el-button
            type
            size="mini"
            :style="{'background-color':leftBtnIndex==2?'#409eff':'','color':leftBtnIndex==2?'#ecf5ff':'','border-color':leftBtnIndex==2?'#409eff':''}"
            @click="changeClickLeft(2)"
          >月</el-button>
          <el-button
            type
            size="mini"
            :style="{'background-color':leftBtnIndex==3?'#409eff':'','color':leftBtnIndex==3?'#ecf5ff':'','border-color':leftBtnIndex==3?'#409eff':''}"
            @click="changeClickLeft(3)"
          >年</el-button>
        </el-button-group>
        <div class="top_case" id="pie_top"></div>
      </el-col>
      <el-col :span="14" style="padding-right:10px;position: relative;">
        <el-button-group class="btn_right">
          <el-button
            type
            size="mini"
            :style="{'background-color':rightBtnIndex==1?'#409eff':'','color':rightBtnIndex==1?'#ecf5ff':'','border-color':rightBtnIndex==1?'#409eff':''}"
            @click="changeClick(1)"
          >周</el-button>
          <el-button
            type
            size="mini"
            :style="{'background-color':rightBtnIndex==2?'#409eff':'','color':rightBtnIndex==2?'#ecf5ff':'','border-color':rightBtnIndex==2?'#409eff':''}"
            @click="changeClick(2)"
          >月</el-button>
          <el-button
            type
            size="mini"
            :style="{'background-color':rightBtnIndex==3?'#409eff':'','color':rightBtnIndex==3?'#ecf5ff':'','border-color':rightBtnIndex==3?'#409eff':''}"
            @click="changeClick(3)"
          >年</el-button>
        </el-button-group>
        <div class="top_case" id="bar_energy"></div>
      </el-col>
      <el-col :span="24" style="padding-top:10px;padding-right:10px;">
        <div class="table_case">
          <h3>设备列表</h3>
          <el-table :data="tableData" size="small" border style="width: 100%">
            <el-table-column prop="deviceNo" label="设备编号" min-width="160" show-overflow-tooltip></el-table-column>
            <el-table-column prop="deviceName" label="设备名称" min-width="200" show-overflow-tooltip></el-table-column>
            <el-table-column prop="yestoday" label="昨日能耗" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="last7Days" label="近7日" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="thisYear" label="本年累计" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="total" label="总能耗" min-width="120" show-overflow-tooltip></el-table-column>
            <el-table-column prop="avg" label="平均值" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="max" label="最大值" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="min" label="最小值" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="state" label="电表采集状态" min-width="100" show-overflow-tooltip></el-table-column>
          </el-table>
          <div style="padding:8px 0;text-align:right;position: relative;">
            <span style="position: absolute;z-index:100;left:4px;">能耗单位：kWh</span>
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
      </el-col>
    </el-col>
  </div>
</template>
<script>
var echarts = require("echarts");
export default {
  data() {
    return {
      enterpriseId: JSON.parse(localStorage.getItem("user")).enterpriseId,
      rightBtnIndex: 1,
      leftBtnIndex: 1,
      pageSize: 10,
      currentPage: 1,
      total: 0,
      tableData: [],
      activeNames: ["1", "2"],
      regionTreeData: [],
      equTypeTreeData: [],
      defaultProps: {
        children: "children",
        label: "name"
      },
      defaultProps1: {
        children: "children",
        label: "categoryName"
      },
      areaId: "",
      typeId: ""
    };
  },
  methods: {
    year(data) {
      const chartYear = echarts.init(document.getElementById("bar_energy"));
      chartYear.setOption({
        title: {
          text: "能耗分析",
          textStyle: {
            fontSize: 14
          }
        },
        color: ["#3398DB"],
        tooltip: {
          trigger: "axis",
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: "shadow" // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        xAxis: [
          {
            type: "category",
            data: data != null ? data.map(item => item.date) : [],
            axisTick: {
              alignWithLabel: true
            }
          }
        ],
        yAxis: [
          {
            type: "value",
            name: "kWh"
          }
        ],
        series: [
          {
            name: "能耗",
            type: "bar",
            barWidth: "60%",
            data: data != null ? data.map(item => item.energy) : []
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartYear.resize();
      });
    },
    equipmentClassfy(data) {
      console.log(data);

      const chartEquipmentClassfy = echarts.init(
        document.getElementById("pie_top"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "能耗TOP榜",
          x: "left",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        legend: {
          orient: "vertical",
          x: "left",
          y: "center",
          data: data.map(item => item.name)
        },
        series: [
          {
            name: "能耗占比",
            type: "pie",
            radius: "55%",
            center: ["50%", "60%"],
            data: data,
            itemStyle: {
              emphasis: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: "rgba(0, 0, 0, 0.5)"
              }
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    changeClick(a) {
      this.rightBtnIndex = a;
      this.getTop10();
      this.energyConsumption();
      this.getTable();
    },
    changeClickLeft(a) {
      this.leftBtnIndex = a;
      this.getTop10();
      this.energyConsumption();
      this.getTable();
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.getTable();
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.getTable();
    },
    treeNodeClick(data) {
      this.areaId = data.id;
      this.getTop10();
      this.energyConsumption();
      this.getTable();
    },
    classifyNodeclick(data) {
      this.typeId = data.id;
      this.getTop10();
      this.energyConsumption();
      this.getTable();
    },
    collapseItemChange(val) {
      console.log(val);
    },
    regionTree() {
      this.Axios(
        {
          params: {
            enterpriseId: this.enterpriseId
          },
          type: "get",
          url: ["/devicePosition/all", "/deviceCategory/all"],
          option: {
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          if (result[0].data.code === 200) {
            // console.log(result[0]);
            let arr = Math.min.apply(
              null,
              result[0].data.data.map(item => {
                return item.parentCode;
              })
            );
            this.regionTreeData = this.filterArray(result[0].data.data, arr);
            console.log(this.regionTreeData);
          }
          if (result[1].data.code === 200) {
            let arr = Math.min.apply(
              null,
              result[1].data.data.map(item => {
                return item.categoryParentNo;
              })
            );
            this.equTypeTreeData = this.filterArray1(result[1].data.data, arr);
            console.log(result[1]);
          }
        },
        ({ type, info }) => {}
      );
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
    filterArray1(data, parent) {
      let vm = this;
      var tree = [];
      var temp;
      for (var i = 0; i < data.length; i++) {
        if (data[i].categoryParentNo == parent) {
          var obj = data[i];
          temp = this.filterArray1(data, data[i].categoryNo);
          if (temp.length > 0) {
            obj.children = temp;
          }
          tree.push(obj);
        }
      }
      return tree;
    },
    getTop10() {
      let url = "";
      if (this.leftBtnIndex == 1) {
        url = "/detail/area/type/week/top10";
      }
      if (this.leftBtnIndex == 2) {
        url = "/detail/area/type/month/top10";
      }
      if (this.leftBtnIndex == 3) {
        url = "/detail/area/type/year/top10";
      }
      this.Axios(
        {
          params: {
            areaId: this.areaId,
            typeId: this.typeId
          },
          type: "get",
          url: url,
          option: {
            requestTarget: "p",
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            console.log(result);
            let arr = [];
            if (result.data.data != null) {
              arr = result.data.data.map(item => {
                return {
                  value: item.total,
                  name: item.deviceName
                };
              });
              this.equipmentClassfy(arr);
            } else {
              this.equipmentClassfy([]);
            }

            // this.topMsg = result.data.data;
          }
        },
        ({ type, info }) => {}
      );
    },
    energyConsumption() {
      let url = "";
      if (this.rightBtnIndex == 1) {
        url = "/detail/area/type/week/everyday";
      }
      if (this.rightBtnIndex == 2) {
        url = "/detail/area/type/month/everyday";
      }
      if (this.rightBtnIndex == 3) {
        url = "/detail/area/type/year/everyMonth";
      }
      this.Axios(
        {
          params: {
            areaId: this.areaId,
            typeId: this.typeId
          },
          type: "get",
          url: url,
          option: {
            requestTarget: "p",
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            console.log(result);
            this.year(result.data.data);
            // let arr = [];
            // arr = result.data.data.map(item => {
            //   return {
            //     value: item.total,
            //     name: item.deviceName
            //   };
            // });
            // this.equipmentClassfy(arr);
            // this.topMsg = result.data.data;
          }
        },
        ({ type, info }) => {}
      );
    },
    getTable() {
      this.Axios(
        {
          params: {
            areaId: this.areaId,
            typeId: this.typeId,
            page: this.currentPage,
            size: this.pageSize
          },
          type: "get",
          url: "/detail/area/type",
          option: {
            requestTarget: "p",
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            console.log(result);
            if (result.data.data != null) {
              this.tableData = result.data.data.content;
              this.total = result.data.data.totalElement;
            } else {
              this.tableData = [];
              this.total = 0;
            }
          }
        },
        ({ type, info }) => {}
      );
    }
  },
  created() {
    this.regionTree();
    this.getTop10();
    this.energyConsumption();
    this.getTable();
  },
  mounted() {
    // this.year();
    // this.equipmentClassfy();
  }
};
</script>
<style lang="less">
@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
@border: 1px solid #dde2eb;
@height: 100vh;
.energy_details {
  overflow: hidden;
  padding-bottom: 10px;
  .left_case {
    width: 100%;
    border: @border;
    border-radius: 4px;
    height: calc(~"100vh - 116px");
    overflow: auto;
    box-shadow: 4px 4px 10px #c0bfbf;
    padding: 10px;
  }
  .top_case {
    width: 100%;
    border: @border;
    border-radius: 4px;
    box-shadow: 4px 4px 10px #c0bfbf;
    height: 400px;
  }
  .table_case {
    width: 100%;
    border: @border;
    border-radius: 4px;
    box-shadow: 4px 4px 10px #c0bfbf;
    min-height: calc(~"100vh - 526px");
    padding: 0 4px;
    h3 {
      padding-left: 4px;
      line-height: 30px;
    }
  }
  .btn_right {
    position: absolute;
    top: 10px;
    right: 20px;
    z-index: 10;
    .el-button:active,
    .el-button:focus,
    .el-button:hover {
      color: #ecf5ff;
      border-color: #409eff;
      background-color: #409eff;
    }
  }
}
</style>