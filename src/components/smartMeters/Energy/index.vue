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
            <el-select size="mini" v-model="value" style="width:80px;" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </span>
        </el-col>
        <el-col :span="24">
          <span>
            <i class="iconfontz" style="color:#349DFF;font-size:16px;">&#xe603;</i>
          </span>
          <span>电表数量：</span>
        </el-col>
        <el-col :span="24">
          <span>
            <i class="iconfontz" style="color:#349DFF;font-size:16px;">&#xe81a;</i>
          </span>
          <span>采集异常数量：</span>
        </el-col>
      </li>
      <li>
        <h4>今日总能耗</h4>
        <el-col :span="6">
          <i class="iconfontz" style="color:#349DFF;font-size:80px;">&#xe63b;</i>
        </el-col>
        <el-col :span="12" style="padding-top:24px;">
          <span style="font-size:36px;padding-left:20px;">3211</span>
          <span>kWh</span>
        </el-col>
        <el-col :span="6" style="padding-top:24px;">
          <span>昨日同比</span>
          <br />
          <br />
          <span style="color:#FF9900;">
            0.9%
            <i class="iconfontz" style="font-size:14px;">&#xe64b;</i>
          </span>
        </el-col>
      </li>
      <li>
        <h4>本月总能耗</h4>
        <el-col :span="6" style="padding-left:8px;">
          <i class="iconfontz" style="color:#349DFF;font-size:72px;">&#xe600;</i>
        </el-col>
        <el-col :span="12" style="padding-top:24px;">
          <span style="font-size:36px;padding-left:20px;">3211</span>
          <span>kWh</span>
        </el-col>
        <el-col :span="6" style="padding-top:24px;">
          <span>上月同比</span>
          <br />
          <br />
          <span style="color:#00CC20;">
            -12.9%
            <i class="iconfontz" style="font-size:14px;">&#xe601;</i>
          </span>
        </el-col>
      </li>
      <li>
        <h4>本年总能耗</h4>
        <el-col :span="6" style="padding-left:8px;">
          <i class="iconfontz" style="color:#349DFF;font-size:72px;">&#xe60b;</i>
        </el-col>
        <el-col :span="18" style="padding-top:24px;">
          <span style="font-size:36px;padding-left:20px;">3211</span>
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
      options: [
        {
          value: "选项1",
          label: "黄金糕"
        },
        {
          value: "选项2",
          label: "双皮奶"
        },
        {
          value: "选项3",
          label: "蚵仔煎"
        },
        {
          value: "选项4",
          label: "龙须面"
        },
        {
          value: "选项5",
          label: "北京烤鸭"
        }
      ],
      value: ""
    };
  },
  methods: {
    today() {
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
          feature: {
            saveAsImage: {}
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
            boundaryGap: false,
            data: ["周一", "周二", "周三", "周四", "周五", "周六", "周日"]
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
            name: "邮件营销",
            type: "line",
            smooth: true,
            // areaStyle: {},
            data: [120, 132, 101, 134, 90, 230, 210],
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
    seven() {
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
          feature: {
            saveAsImage: {}
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
            boundaryGap: false,
            data: ["周一", "周二", "周三", "周四", "周五", "周六", "周日"]
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
            name: "邮件营销",
            type: "line",
            smooth: true,
            // areaStyle: {},
            data: [120, 132, 101, 134, 90, 230, 210],
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
    year() {
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
            data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
            axisTick: {
              alignWithLabel: true
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
            name: "直接访问",
            type: "bar",
            barWidth: "60%",
            data: [10, 52, 200, 334, 390, 330, 220]
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartYear.resize();
      });
    },
    equipmentClassfy() {
      const chartEquipmentClassfy = echarts.init(
        document.getElementById("equipment_classfy"),
        "light"
      );
      chartEquipmentClassfy.setOption({
        title: {
          text: "按设备类别占比分析 TOP 5",
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
          data: [
            "rose1",
            "rose2",
            "rose3",
            "rose4",
            "rose5",
            "rose6",
            "rose7",
            "rose8"
          ]
        },
        toolbox: {
          show: true
        },
        calculable: true,
        series: [
          {
            name: "半径模式",
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
            data: [
              { value: 10, name: "rose1" },
              { value: 5, name: "rose2" },
              { value: 15, name: "rose3" },
              { value: 25, name: "rose4" },
              { value: 20, name: "rose5" },
              { value: 35, name: "rose6" },
              { value: 30, name: "rose7" },
              { value: 40, name: "rose8" }
            ]
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentClassfy.resize();
      });
    },
    EquipmentComparison() {
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
          data: ["邮件营销", "联盟广告", "视频广告", "直接访问", "搜索引擎"]
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        xAxis: {
          type: "category",
          boundaryGap: false,
          data: ["周一", "周二", "周三", "周四", "周五", "周六", "周日"]
        },
        yAxis: {
          type: "value"
        },
        series: [
          {
            name: "邮件营销",
            type: "line",
            stack: "总量",
            data: [120, 132, 101, 134, 90, 230, 210]
          },
          {
            name: "联盟广告",
            type: "line",
            stack: "总量",
            data: [220, 182, 191, 234, 290, 330, 310]
          },
          {
            name: "视频广告",
            type: "line",
            stack: "总量",
            data: [150, 232, 201, 154, 190, 330, 410]
          },
          {
            name: "直接访问",
            type: "line",
            stack: "总量",
            data: [320, 332, 301, 334, 390, 330, 320]
          },
          {
            name: "搜索引擎",
            type: "line",
            stack: "总量",
            data: [820, 932, 901, 934, 1290, 1330, 1320]
          }
        ]
      });
      window.addEventListener("resize", () => {
        chartEquipmentComparison.resize();
      });
    }
  },
  created() {},
  mounted() {
    this.today();
    this.seven();
    this.year();
    this.equipmentClassfy();
    this.EquipmentComparison();
  }
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