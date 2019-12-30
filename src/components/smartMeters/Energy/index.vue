<template>
  <div class="general_situation">
    <ul class="top_case">
      <li>
        <h4>基本信息</h4>
        <el-col :span="24" style="padding:0 24px;">
          <span>
            <i class="iconfontz" style="color:#349DFF;font-size:16px;">&#xe655;</i>
          </span>
          <span>
            统计区域：
            <el-cascader
              filterable
              :show-all-levels="false"
              size="mini"
              v-model="value"
              :options="options"
              :props="{ expandTrigger: 'hover',label:'name',value:'id',checkStrictly: true }"
              @change="handleChange"
              style="width:80px;"
            ></el-cascader>
          </span>
        </el-col>
        <el-col :span="24">
          <span>
            <i class="iconfontz" style="color:#349DFF;font-size:16px;">&#xe603;</i>
          </span>
          <span>电表数量：{{sumMsg.num}}</span>
        </el-col>
        <el-col :span="24">
          <span>
            <i class="iconfontz" style="color:#349DFF;font-size:16px;">&#xe81a;</i>
          </span>
          <span>采集异常数量：{{sumMsg.errorNum}}</span>
        </el-col>
      </li>
      <li>
        <h4>今日总能耗</h4>
        <el-col :span="6">
          <i class="iconfontz" style="color:#349DFF;font-size:80px;">&#xe63b;</i>
        </el-col>
        <el-col :span="12" style="padding-top:24px;">
          <span style="font-size:36px;padding-left:20px;">{{topMsg[0]!=null?topMsg[0].dayEnergy:0}}</span>
          <span>kWh</span>
        </el-col>
        <el-col :span="6" style="padding-top:24px;">
          <span>昨日同比</span>
          <br />
          <br />
          <span style="color:#FF9900;" v-if="topMsg[0]!=null?topMsg[0].type==1:false">
            {{topMsg[0]!=null?topMsg[0].dayRate:0}}
            <i
              class="iconfontz"
              style="font-size:14px;"
            >&#xe64b;</i>
          </span>
          <span style="color:#00CC20;" v-if="topMsg[0]!=null?topMsg[0].type==0:false">
            {{topMsg[0]!=null?topMsg[0].dayRate:0}}
            <i
              class="iconfontz"
              style="font-size:14px;"
            >&#xe601;</i>
          </span>
        </el-col>
      </li>
      <li>
        <h4>本月总能耗</h4>
        <el-col :span="6" style="padding-left:8px;">
          <i class="iconfontz" style="color:#349DFF;font-size:72px;">&#xe600;</i>
        </el-col>
        <el-col :span="12" style="padding-top:24px;">
          <span
            style="font-size:36px;padding-left:20px;"
          >{{topMsg[1]!=null?topMsg[1].monthEnergy:0}}</span>
          <span>kWh</span>
        </el-col>
        <el-col :span="6" style="padding-top:24px;">
          <span>上月同比</span>
          <br />
          <br />
          <span style="color:#FF9900;" v-if="topMsg[1]!=null?topMsg[1].type==1:false">
            {{topMsg[1]!=null?topMsg[1].monthRate:0}}
            <i
              class="iconfontz"
              style="font-size:14px;"
            >&#xe64b;</i>
          </span>
          <span style="color:#00CC20;" v-if="topMsg[1]!=null?topMsg[1].type==0:false">
            {{topMsg[1]!=null?topMsg[1].monthRate:0}}
            <i
              class="iconfontz"
              style="font-size:14px;"
            >&#xe601;</i>
          </span>
        </el-col>
      </li>
      <li>
        <h4>本年总能耗</h4>
        <el-col :span="6" style="padding-left:8px;">
          <i class="iconfontz" style="color:#349DFF;font-size:72px;">&#xe60b;</i>
        </el-col>
        <el-col :span="18" style="padding-top:24px;">
          <span style="font-size:36px;padding-left:20px;">{{topMsg[2]!=null?topMsg[2].yearEnergy:0}}</span>
          <span>kWh</span>
        </el-col>
      </li>
    </ul>
    <div class="center_case">
      <div class="echars_case" id="today"></div>
      <div class="echars_case" id="seven"></div>
      <div class="echars_case" id="year"></div>
    </div>
    <div class="tottom_case">
      <div class="left_case" id="equipment_classfy"></div>
      <div class="right_case" id="Equipment_comparison"></div>
    </div>
  </div>
</template>
<script>
var echarts = require("echarts");
export default {
  data() {
    return {
      options: [],
      value: [],
      enterpriseId: JSON.parse(localStorage.getItem("user")).enterpriseId,
      areaId: "",
      topMsg: [
        { dayEnergy: "", dayRate: "", type: "" },
        { monthEnergy: "", monthRate: "", type: "" },
        { yearEnergy: "" }
      ],
      todayMsg: [],
      sevenDayMsg: [],
      yearMsg: [],
      topTenMsg: [],
      classifyMsg: [],
      sumMsg: {}
    };
  },
  methods: {
    handleChange(value) {
      this.areaId = value[value.length - 1];
      this.getAllMsg();
      // console.log(this.areaId);
    },
    today(value) {
      const chartToday = echarts.init(document.getElementById("today"));
      chartToday.setOption({
        title: {
          text: "今日用电量",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985"
            }
          }
        },
        toolbox: {
          // feature: {
          //   saveAsImage: {}
          // }
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
            boundaryGap: false,
            data: value.map(item => item.hour)
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
            name: "用电量",
            type: "line",
            smooth: true,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgb(24, 144, 255)"
                },
                {
                  offset: 1,
                  color: "rgb(255, 255, 255)"
                }
              ])
            },
            data: value.map(item => item.energy),
            itemStyle: {
              color: "#0A82C8"
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartToday.resize();
      });
    },
    seven(value) {
      const chartSeven = echarts.init(document.getElementById("seven"));
      chartSeven.setOption({
        title: {
          text: "近七天用电量",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985"
            }
          }
        },
        toolbox: {
          // feature: {
          //   saveAsImage: {}
          // }
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
            boundaryGap: false,
            data: value.map(item => item.date)
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
            name: "用电量",
            type: "line",
            smooth: true,
            // areaStyle: {
            //   color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            //     {
            //       offset: 0,
            //       color: "rgb(24, 144, 255)"
            //     },
            //     {
            //       offset: 1,
            //       color: "rgb(255, 255, 255)"
            //     }
            //   ])
            // },
            data: value.map(item => item.energy),
            itemStyle: {
              color: "#0A82C8"
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartSeven.resize();
      });
    },
    year(value) {
      const chartYear = echarts.init(document.getElementById("year"));
      chartYear.setOption({
        title: {
          text: "本年用电",
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
            name: "用电量",
            type: "bar",
            barWidth: "60%",
            data: value.map(item => item.energy)
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartYear.resize();
      });
    },
    equipmentClassfy(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("equipment_classfy"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "按设备类别占比分析 TOP 10",
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
          data: value.map(item => item.deviceName)
        },
        toolbox: {
          show: true
        },
        calculable: true,
        series: [
          {
            name: "设备类别耗电占比",
            type: "pie",
            radius: [20, 110],
            center: ["50%", "50%"],
            roseType: "radius",
            label: {
              normal: {
                show: false
              },
              emphasis: {
                show: true
              }
            },
            lableLine: {
              normal: {
                show: false
              },
              emphasis: {
                show: true
              }
            },
            data: value.map(item => {
              return {
                value: item.total,
                name: item.deviceName
              };
            })
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    EquipmentComparison(value) {
      console.log(value[1]);
      const chartEquipmentComparison = echarts.init(
        document.getElementById("Equipment_comparison"),
        "light"
      );
      chartEquipmentComparison.setOption({
        title: {
          text: "按设备类别分析对比",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          trigger: "axis"
        },
        legend: {
          data: value.length > 0 ? value[1].value.map(item => item.name) : []
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        toolbox: {
          // feature: {
          //   saveAsImage: {}
          // }
        },
        xAxis: {
          type: "category",
          boundaryGap: false,
          data: value.length > 0 ? value[0].date : []
        },
        yAxis: {
          type: "value",
          name: "kWh"
        },
        series:
          value.length > 0
            ? value[1].value
            : [{ name: "", data: [], type: "line" }]
      });
      window.addEventListener("resize", () => {
        chartEquipmentComparison.resize();
      });
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
            // console.log(result);
            let arr = Math.min.apply(
              null,
              result.data.data.map(item => {
                return item.parentCode;
              })
            );
            this.options = this.filterArray(result.data.data, arr);
            this.areaId = this.options[0].id;
            this.value.push(this.areaId);
            this.getAllMsg();
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
    getAllMsg() {
      this.Axios(
        {
          params: {
            areaId: this.areaId
          },
          type: "get",
          url: [
            "/energy/area/total",
            "/energy/area/today/everyHour",
            "/energy/area/7days/everyDay",
            "/energy/area/year/everyMonth",
            "/energy/area/30day/top10",
            "/energy/area/month/type",
            "/meter/getMeterNum"
          ],
          option: {
            requestTarget: "p",
            enableMsg: false
          }
        },
        this
      ).then(
        result => {
          // console.log(result);
          if (result[0].data.code === 200) {
            this.topMsg = result[0].data.data;
            if (
              result[0].data.data[0] != null &&
              result[0].data.data[1] != null &&
              result[0].data.data[2] != null
            ) {
              if (this.topMsg[0].dayRate.indexOf("-") != -1) {
                this.topMsg[0].type = 0;
              } else {
                this.topMsg[0].type = 1;
              }
              if (this.topMsg[1].monthRate.indexOf("-") != -1) {
                this.topMsg[1].type = 0;
              } else {
                this.topMsg[1].type = 1;
              }
            }
          }
          if (result[1].data.code === 200) {
            this.todayMsg = result[1].data.data;
            if (this.todayMsg != null) {
              this.today(this.todayMsg);
            } else {
              this.today([]);
            }
          }
          if (result[2].data.code === 200) {
            this.sevenDayMsg = result[2].data.data;
            if (this.sevenDayMsg != null) {
              this.seven(this.sevenDayMsg);
            } else {
              this.seven([]);
            }
          }
          if (result[3].data.code === 200) {
            this.yearMsg = result[3].data.data;
            if (this.yearMsg != null) {
              this.year(this.yearMsg);
            } else {
              this.year([]);
            }
          }
          if (result[4].data.code === 200) {
            this.topTenMsg = result[4].data.data;
            if (this.topTenMsg != null) {
              this.equipmentClassfy(this.topTenMsg);
            } else {
              this.equipmentClassfy([]);
            }
          }
          if (result[5].data.code === 200) {
            this.classifyMsg = result[5].data.data;
            if (this.classifyMsg != null) {
              this.classifyMsg[1].value = this.classifyMsg[1].value.map(
                item => {
                  return {
                    name: item.type,
                    type: "line",
                    stack: "总量",
                    data: item.energy
                  };
                }
              );
              this.EquipmentComparison(this.classifyMsg);
            } else {
              this.EquipmentComparison([]);
            }

            // console.log(this.classifyMsg);
            // console.log(this.classifyMsg.map(item => item.date));
          }
          if (result[6].data.code === 200) {
            this.sumMsg = result[6].data.data;
          }
        },
        ({ type, info }) => {}
      );
    }
  },
  created() {
    this.allOrganize();
  },
  mounted() {}
};
</script>
<style lang="less">
.general_situation {
  .top_case {
    padding-bottom: 12px;
    display: flex;
    justify-content: space-between;
    li {
      list-style-type: none;
      width: 24.5%;
      border: 1px solid #eceef1;
      border-radius: 4px;
      height: 130px;
      box-shadow: 4px 4px 10px #c0bfbf;
      overflow: hidden;
      h4 {
        line-height: 32px;
        padding: 0 12px;
      }
      .el-col-24 {
        padding: 6px 24px;
      }
    }
  }
  .center_case {
    display: flex;
    justify-content: space-between;
    padding-bottom: 12px;
    .echars_case {
      padding: 4px 0;
      width: 33%;
      height: 370px;
      display: inline-block;
      border: 1px solid #eceef1;
      border-radius: 4px;
      box-shadow: 4px 4px 10px #c0bfbf;
    }
  }
  .tottom_case {
    display: flex;
    justify-content: space-between;
    padding-bottom: 12px;
    .left_case {
      padding: 4px 0;
      width: 33%;
      height: 310px;
      display: inline-block;
      border: 1px solid #eceef1;
      border-radius: 4px;
      box-shadow: 4px 4px 10px #c0bfbf;
    }
    .right_case {
      padding: 4px 0;
      width: 66.5%;
      height: 310px;
      display: inline-block;
      border: 1px solid #eceef1;
      border-radius: 4px;
      box-shadow: 4px 4px 10px #c0bfbf;
    }
  }
  .el-input--mini .el-input__inner {
    border: none;
    padding-left: 0;
    color: #349dff;
  }
}
</style>