<template>
  <div class="efficiency_details">
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
        <div class="top_case" id="oee_detail_pie_top"></div>
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
        <div class="top_case" id="pee_bar_energy"></div>
      </el-col>
      <el-col :span="24" style="padding-top:10px;padding-right:10px;">
        <div class="table_case">
          <h3>设备列表</h3>
          <el-table :data="tableData" size="small" border style="width: 100%">
            <el-table-column prop="deviceId" label="设备编号" min-width="160" show-overflow-tooltip></el-table-column>
            <el-table-column prop="deviceName" label="设备名称" min-width="200" show-overflow-tooltip></el-table-column>
            <el-table-column prop="oeeOfLastDay" label="昨日" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column prop="oeeOfLastWeek" label="近7日" min-width="100" show-overflow-tooltip></el-table-column>
            <el-table-column
              prop="oeeOfLastMonth"
              label="近30天"
              min-width="100"
              show-overflow-tooltip
            ></el-table-column>
            <el-table-column prop="oeeOfLastYear" label="近1年" min-width="120" show-overflow-tooltip></el-table-column>
            <el-table-column
              prop="oeeOfMinInTotal"
              label="最小值"
              min-width="100"
              show-overflow-tooltip
            ></el-table-column>
            <el-table-column
              prop="oeeOfMaxInTotal"
              label="最大值"
              min-width="100"
              show-overflow-tooltip
            ></el-table-column>
            <el-table-column label="操作" width="80" show-overflow-tooltip>
              <template slot-scope="scope">
                <el-button type="text" @click="detailsRow(scope.row)">详情</el-button>
              </template>
            </el-table-column>
          </el-table>
          <div style="padding:8px 0;text-align:right;position: relative;">
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
      regionId: "",
      typeId: "",
      leftTime: "WEEK",
      rightTime: "WEEK"
    };
  },
  methods: {
    detailsRow(row) {
      console.log(row);
    },
    year(value) {
      const chartYear = echarts.init(document.getElementById("pee_bar_energy"));
      chartYear.setOption({
        title: {
          text: "OEE 分析",
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
            data: value.map(item => item.date),
            axisTick: {
              alignWithLabel: true
            },
            axisLabel: {
              interval: 0,
              rotate: value.length > 10 ? 45 : 0 //倾斜度 -90 至 90 默认为0
            }
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ],
        series: [
          {
            name: "OEE 分析",
            type: "bar",
            barMaxWidth: "50px",
            data: value.map(item => item.oee)
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartYear.resize();
      });
    },
    equipmentClassfy(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("oee_detail_pie_top"),
        "light"
      );
      var myColor = [
        // "#eb2100",
        // "#eb3600",
        // "#d0570e",
        // "#d0a00e",
        // "#34da62",
        "#00e9db",
        "#00c0e9",
        "#0096f3",
        "#33CCFF",
        "#33FFCC"
      ];
      chartEquipmentClassfy.setOption({
        title: {
          text: "OEE TOP 5",
          x: "left",
          textStyle: {
            fontSize: 14
          }
        },
        // tooltip: {
        //   trigger: "axis"
        // },
        // backgroundColor: "#0e2147",
        grid: {
          left: "11%",
          top: "12%",
          right: "0%",
          bottom: "8%",
          containLabel: true
        },
        xAxis: [
          {
            show: false
          }
        ],
        yAxis: [
          {
            axisTick: "none",
            axisLine: "none",
            offset: "27",
            axisLabel: {
              textStyle: {
                color: "#000000",
                fontSize: "14"
              }
            },
            data: value.map(item => item.deviceName)
          },
          {
            axisTick: "none",
            axisLine: "none",
            axisLabel: {
              textStyle: {
                color: "#000000",
                fontSize: "14"
              }
            },
            data: ["100%", "100%", "100%", "100%", "100%"].slice(
              0,
              value.length
            )
          },
          {
            name: "",
            nameGap: "50",
            nameTextStyle: {
              color: "#ffffff",
              fontSize: "16"
            },
            axisLine: {
              lineStyle: {
                color: "rgba(0,0,0,0)"
              }
            },
            data: []
          }
        ],
        series: [
          {
            name: "条",
            type: "bar",
            yAxisIndex: 0,
            data: value.map(item => item.oee),
            label: {
              normal: {
                show: true,
                position: "right",
                textStyle: {
                  color: "#000000",
                  fontSize: "14"
                }
              }
            },
            barWidth: 12,
            itemStyle: {
              normal: {
                color: function(params) {
                  var num = myColor.length;
                  return myColor[params.dataIndex % num];
                }
              }
            },
            z: 10
          },
          {
            name: "白框",
            type: "bar",
            yAxisIndex: 1,
            barGap: "-100%",
            data: [99.5, 99.5, 99.5, 99.5, 99.5].slice(0, value.length),
            barWidth: 20,
            itemStyle: {
              normal: {
                color: "#ffffff",
                barBorderRadius: 5
              }
            },
            z: 9
          },
          {
            name: "外框",
            type: "bar",
            yAxisIndex: 2,
            barGap: "-100%",
            data: [100, 100, 100, 100, 100].slice(0, value.length),
            barWidth: 24,
            itemStyle: {
              normal: {
                color: function(params) {
                  var num = myColor.length;
                  return myColor[params.dataIndex % num];
                },
                barBorderRadius: 5
              }
            },
            z: 0
          },
          {
            name: "外圆",
            type: "scatter",
            hoverAnimation: false,
            data: [0, 0, 0, 0, 0].slice(0, value.length),
            yAxisIndex: 2,
            symbolSize: 35,
            itemStyle: {
              normal: {
                color: function(params) {
                  var num = myColor.length;
                  return myColor[params.dataIndex % num];
                },
                opacity: 1
              }
            },
            z: 2
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    changeClick(a) {
      this.rightBtnIndex = a;
      if (a == 1) {
        this.rightTime = "WEEK";
        this.getOEE();
      }
      if (a == 2) {
        this.rightTime = "MONTH";
        this.getOEE();
      }
      if (a == 3) {
        this.rightTime = "YEAR";
        this.getOEE();
      }
    },
    changeClickLeft(a) {
      this.leftBtnIndex = a;
      if (a == 1) {
        this.leftTime = "WEEK";
        this.getTop5();
      }
      if (a == 2) {
        this.leftTime = "MONTH";
        this.getTop5();
      }
      if (a == 3) {
        this.leftTime = "YEAR";
        this.getTop5();
      }
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
      this.regionId = data.id;
      // console.log(data);
      this.getTop5();
      this.getOEE();
      this.getTable();
    },
    classifyNodeclick(data) {
      this.typeId = data.id;
      // console.log(data);
      this.getTop5();
      this.getOEE();
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
          if (result[0].data.code === 200 && result[1].data.code === 200) {
            // console.log(result[0]);
            let arr = Math.min.apply(
              null,
              result[0].data.data.map(item => {
                return item.parentCode;
              })
            );
            this.regionTreeData = this.filterArray(result[0].data.data, arr);
            this.regionId = this.regionTreeData[0].id;
            let arr1 = Math.min.apply(
              null,
              result[1].data.data.map(item => {
                return item.categoryParentNo;
              })
            );
            this.equTypeTreeData = this.filterArray1(result[1].data.data, arr1);
            this.typeId = "all";
            this.getTop5();
            this.getOEE();
            this.getTable();
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
    getTop5() {
      this.Axios(
        {
          params: {},
          type: "get",
          url:
            "/v1/oee/top5/" +
            this.leftTime +
            "/" +
            this.regionId +
            "/" +
            this.typeId,
          option: {
            requestTarget: "s",
            enableMsg: false
          },
          config: {
            headers: { "Content-Type": "application/json" }
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            this.equipmentClassfy(result.data.data);
            console.log(result);
            // this.topMsg = result[0].data.data;
          }
        },
        ({ type, info }) => {}
      );
    },
    getOEE() {
      this.Axios(
        {
          params: {},
          type: "get",
          url:
            "/v1/oee/total/" +
            this.rightTime +
            "/" +
            this.regionId +
            "/" +
            this.typeId,
          option: {
            requestTarget: "s",
            enableMsg: false
          },
          config: {
            headers: { "Content-Type": "application/json" }
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            this.year(result.data.data);
            console.log(result);
            // this.topMsg = result[0].data.data;
          }
        },
        ({ type, info }) => {}
      );
    },
    getTable() {
      this.Axios(
        {
          params: {
            page: this.currentPage,
            size: this.pageSize
          },
          type: "get",
          url: "/v1/oee/abstract/" + this.regionId + "/" + this.typeId,
          option: {
            requestTarget: "s",
            enableMsg: false
          },
          config: {
            headers: { "Content-Type": "application/json" }
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result.data.code === 200) {
            console.log(result);
            this.tableData = [...result.data.data.data];
            this.total = result.data.data.total;
            // this.topMsg = result[0].data.data;
          }
        },
        ({ type, info }) => {}
      );
    }
  },
  created() {
    this.regionTree();
  },
  mounted() {}
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
.efficiency_details {
  .el-button--text {
    padding: 4px;
  }
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