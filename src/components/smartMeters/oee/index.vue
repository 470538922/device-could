<template>
  <div class="oee_list">
    <el-col :span="24">
      <span>数据分析：</span>
      <el-button
        @click="topChangeBtn(1)"
        :style="{'background-color':topBtnIndex==1?'#409eff':'','color':topBtnIndex==1?'#ecf5ff':'','border-color':topBtnIndex==1?'#409eff':''}"
        size="mini"
      >最近1周</el-button>
      <el-button
        @click="topChangeBtn(2)"
        :style="{'background-color':topBtnIndex==2?'#409eff':'','color':topBtnIndex==2?'#ecf5ff':'','border-color':topBtnIndex==2?'#409eff':''}"
        size="mini"
      >最近1个月</el-button>
      <!-- <el-button
        @click="topChangeBtn(3)"
        :style="{'background-color':topBtnIndex==3?'#409eff':'','color':topBtnIndex==3?'#ecf5ff':'','border-color':topBtnIndex==3?'#409eff':''}"
        size="mini"
      >最近3个月</el-button>-->
      <el-button
        @click="topChangeBtn(4)"
        :style="{'background-color':topBtnIndex==4?'#409eff':'','color':topBtnIndex==4?'#ecf5ff':'','border-color':topBtnIndex==4?'#409eff':''}"
        size="mini"
      >最近6个月</el-button>
      <el-button
        @click="topChangeBtn(5)"
        :style="{'background-color':topBtnIndex==5?'#409eff':'','color':topBtnIndex==5?'#ecf5ff':'','border-color':topBtnIndex==5?'#409eff':''}"
        size="mini"
      >最近12个月</el-button>
    </el-col>
    <el-col :span="7" style="padding:10px 0">
      <div class="top_case" id="oee_pie_top"></div>
    </el-col>
    <el-col :span="17" style="padding:10px 0 10px 10px;">
      <div class="top_case">
        <el-col :span="6">
          <div class="item_gauge" id="gauge1"></div>
        </el-col>
        <el-col :span="6">
          <div class="item_gauge" id="gauge2"></div>
        </el-col>
        <el-col :span="6">
          <div class="item_gauge" id="gauge3"></div>
        </el-col>
        <el-col :span="6">
          <div class="item_gauge" id="gauge4"></div>
        </el-col>
      </div>
    </el-col>
    <el-col :span="24">
      <div class="bottim_case" id="oee"></div>
    </el-col>
  </div>
</template>
<script>
var echarts = require("echarts");
export default {
  data() {
    return {
      topBtnIndex: 1,
      time: "WEEK"
    };
  },
  methods: {
    topChangeBtn(a) {
      this.topBtnIndex = a;
      if (a == 1) {
        this.time = "WEEK";
        this.getAllMsg();
      }
      if (a == 2) {
        this.time = "MONTH";
        this.getAllMsg();
      }
      if (a == 3) {
        this.time = "THREE_MONTH";
        this.getAllMsg();
      }
      if (a == 4) {
        this.time = "SEX_MONTH";
        this.getAllMsg();
      }
      if (a == 5) {
        this.time = "YEAR";
        this.getAllMsg();
      }
    },
    equipmentClassfy(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("oee_pie_top"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "OEE占比",
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
          data: value.map(item => {
            return item.des + "%";
          })
        },
        series: [
          {
            name: "OEE占比",
            type: "pie",
            radius: "55%",
            center: ["50%", "60%"],
            data: value.map(item => {
              return {
                value: item.count,
                name: item.des + "%"
              };
            }),
            // [
            //   { value: 335, name: "直接访问" },
            //   { value: 310, name: "邮件营销" },
            //   { value: 234, name: "联盟广告" },
            //   { value: 135, name: "视频广告" },
            //   { value: 1548, name: "搜索引擎" }
            // ]
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
    gauge1(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("gauge1"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "时间开动率",
          x: "center",
          y: "75%",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          formatter: "{a} <br/>{b} : {c}%"
        },
        toolbox: {
          // feature: {
            // restore: {},
            // saveAsImage: {}
          // }
        },
        series: [
          {
            name: "时间开动率",
            type: "gauge",
            detail: {
              formatter: "{value}%",
              textStyle: {
                fontSize: 14
              },
              offsetCenter: ["0%", "65%"]
            },
            title: {
              show: false
            },
            data: [{ value: value, name: "时间开动率" }],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [[0.5, "#FE0302"], [0.75, "#FECB39"], [1, "#10CF46"]]
              }
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    gauge2(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("gauge2"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "性能开动率",
          x: "center",
          y: "75%",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          formatter: "{a} <br/>{b} : {c}%"
        },
        toolbox: {
          feature: {
            // restore: {},
            // saveAsImage: {}
          }
        },
        series: [
          {
            name: "性能开动率",
            type: "gauge",
            detail: {
              formatter: "{value}%",
              textStyle: {
                fontSize: 14
              },
              offsetCenter: ["0%", "65%"]
            },
            title: {
              show: false
            },
            data: [{ value: value, name: "性能开动率" }],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [[0.5, "#FE0302"], [0.75, "#FECB39"], [1, "#10CF46"]]
              }
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    gauge3(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("gauge3"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "良品率",
          x: "center",
          y: "75%",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          formatter: "{a} <br/>{b} : {c}%"
        },
        toolbox: {
          feature: {
            // restore: {},
            // saveAsImage: {}
          }
        },
        series: [
          {
            name: "良品率",
            type: "gauge",
            detail: {
              formatter: "{value}%",
              textStyle: {
                fontSize: 14
              },
              offsetCenter: ["0%", "65%"]
            },
            title: {
              show: false
            },
            data: [{ value: value, name: "良品率" }],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [[0.5, "#FE0302"], [0.75, "#FECB39"], [1, "#10CF46"]]
              }
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    gauge4(value) {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("gauge4"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "OEE",
          x: "center",
          y: "75%",
          textStyle: {
            fontSize: 14
          }
        },
        tooltip: {
          formatter: "{a} <br/>{b} : {c}%"
        },
        toolbox: {
          feature: {
            // restore: {},
            // saveAsImage: {}
          }
        },
        series: [
          {
            name: "OEE",
            type: "gauge",
            detail: {
              formatter: "{value}%",
              textStyle: {
                fontSize: 14
              },
              offsetCenter: ["0%", "65%"]
            },
            title: {
              show: false
            },
            data: [{ value: value, name: "OEE" }],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [[0.5, "#FE0302"], [0.75, "#FECB39"], [1, "#10CF46"]]
              }
            }
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    oee(value) {
      const chartYear = echarts.init(document.getElementById("oee"));
      chartYear.setOption({
        title: {
          text: "OEE趋势",
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
              rotate: value.length > 25 ? 45 : 0 //倾斜度 -90 至 90 默认为0
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
            name: "OEE趋势",
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
    getAllMsg() {
      this.Axios(
        {
          params: {
            areaId: this.areaId
          },
          type: "get",
          url: [
            "/v1/oee/bef/" + this.time,
            "/v1/oee/ratio/" + this.time,
            "/v1/oee/total/trend/" + this.time
          ],
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
          if (result[0].data.code === 200) {
            this.gauge1(result[0].data.data.timePercent);
            this.gauge2(result[0].data.data.perfPercent);
            this.gauge3(result[0].data.data.passPercent);
            this.gauge4(result[0].data.data.oee);
            // this.topMsg = result[0].data.data;
          }
          if (result[1].data.code === 200) {
            // console.log(result[1].data.data);
            this.equipmentClassfy(result[1].data.data);
            // console.log(result[1].data.data);
            // this.todayMsg = result[1].data.data;
            // this.today(this.todayMsg);
          }
          if (result[2].data.code === 200) {
            console.log(result[2].data.data);
            this.oee(result[2].data.data);
            // this.sevenDayMsg = result[2].data.data;
            // this.seven(this.sevenDayMsg);
          }
        },
        ({ type, info }) => {}
      );
    }
  },
  created() {
    this.getAllMsg();
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
.oee_list {
  padding: 10px;
  overflow: hidden;
  .el-button:active,
  .el-button:focus,
  .el-button:hover {
    color: #ecf5ff;
    border-color: #409eff;
    background-color: #409eff;
  }
  .top_case {
    width: 100%;
    border: @border;
    border-radius: 4px;
    box-shadow: 4px 4px 10px #c0bfbf;
    height: 330px;
    .item_gauge {
      width: 100%;
      height: 330px;
    }
  }
  .bottim_case {
    width: 100%;
    border: @border;
    border-radius: 4px;
    box-shadow: 4px 4px 10px #c0bfbf;
    height: 460px;
  }
}
</style>