<template>
  <div class="home">
    <!-- 页面标题开始 -->
    <div class="title">
      <div class="titleCenter">集团资产大屏</div>
      <div class="none"></div>
      <div titleRight class="titleRight">资产总数 过保数 异常数 在线数</div>
    </div>
    <!-- 页面标题结束 -->
    <!-- 顶部开始 -->
    <div class="header">
      <div class="box"></div>
      <div class="box">资产总数：9060</div>
      <div class="box">在线数：9000</div>
      <div class="box">过保属：500</div>
      <div class="box">异常数：100</div>
      <div class="box"></div>
      <div class="box"></div>
    </div>
    <!-- 顶部结束 -->
    <!-- 素拓图和标题开始 -->
    <div class="main1">
      <div class="sutuo">
        <!-- 素拓图上的标题开始  -->
        <div class="tip">
          <div class="tip1">服务器</div>
          <div class="tip1">网络设备</div>
          <div class="tip1">安全设备</div>
          <div class="tip1">动环设备</div>
        </div>
        <!-- 素拓图上的标题结束 -->
        <div id="sutuo"></div>
      </div>
      <!-- 中国地图开始 -->
      <div class="map">
        <div id="map"></div>
      </div>
      <!-- 中国地图结束-->
    </div>
    <!-- 素拓图和标题结束 -->
    <!-- 底部资产及仪表盘开始 -->
    <div class="footer">
      <!-- line开始 -->
      <div class="footerBox line">
        <div id="line"></div>
        <div class="tip2">资产运行态势</div>
        <div class="tip">
          <div class="tip1">资产总数</div>
          <div class="tip1">在线数</div>
          <div class="tip1">过保数</div>
          <div class="tip1">异常数</div>
        </div>
      </div>
      <!-- line结束 -->
      <!-- 仪表盘开始 -->
      <div class="footerBox gague">
        <div id="gague"></div>
      </div>
      <!-- 仪表盘结束 -->
      <div class="footerBox"></div>
    </div>
    <!-- 底部资产及仪表盘结束 -->
  </div>
</template>

<script>
// @ is an alias to /src
import "../assets/js/china";
export default {
  mounted() {
    this.sutuo();
    this.line();
    this.gague();
    this.map();
  },
  methods: {
    // 素拓
    sutuo() {
      var echarts = require("echarts");
      var myChart = echarts.init(document.getElementById("sutuo"));
      var colors = ["#3CB371", "#3CB371", "#3CB371", "#FFA500", "#DC143C"];
      var getdata = function getData() {
        let data = {
          name: "场站",
          value: 0,
          children: [],
        };
        for (let i = 1; i <= 5; i++) {
          let obj = {
            name: i + "#箱变",
            value: i,
            children: [],
          };
          for (let j = 1; j <= 20; j++) {
            let obj2 = {
              name: `逆变器-${i}-${j}`,
              value: 1 + "-" + i + "-" + j,
            };
            if (j % 2 == 1) {
              obj2.children = [];
              for (let k = 1; k <= 14; k++) {
                let obj3 = {
                  name: `组串-${i}-${j}-${k}`,
                  value: 1 + "-" + i + "-" + j + "-" + k,
                };
                obj2.children.push(obj3);
              }
            }

            obj.children.push(obj2);
          }

          data.children.push(obj);
        }
        let arr = [];
        arr.push(data);
        //
        arr = handle(arr, 0);
        console.log(arr);
        return arr;
      };
      var handle = function handleData(data, index, color = "#00f6ff") {
        //index标识第几层
        return data.map((item, index2) => {
          //计算出颜色
          if (index == 1) {
            color = colors.find((item, eq) => eq == index2 % 5);
          }
          // 设置节点大小
          if (index === 0 || index === 1) {
            item.label = {
              position: "inside",
              //   rotate: 0,
              //   borderRadius: "50%",
            };
          }
          // 设置label大小
          switch (index) {
            case 0:
              item.symbolSize = 70;
              break;
            case 1:
              item.symbolSize = 20;
              break;
            default:
              item.symbolSize = 10;
              break;
          }
          // 设置线条颜色
          item.lineStyle = {
            color: color,
          };

          if (item.children) {
            //存在子节点
            item.itemStyle = {
              borderColor: color,
              color: color,
            };
            item.children = handle(item.children, index + 1, color);
          } else {
            //不存在
            item.itemStyle = {
              color: "transparent",
              borderColor: color,
            };
          }
          return item;
        });
      };
      var option = {
        type: "tree",
        backgroundColor: "#000",
        // grid: {
        //   right: "35%",
        //   left: "35%",
        //   top: "35%",
        //   bottom: "35%",
        // },
        toolbox: {
          //工具栏
          show: true,
          iconStyle: {
            borderColor: "#03ceda",
          },
          feature: {
            restore: {},
          },
        },
        tooltip: {
          //提示框
          trigger: "item",
          triggerOn: "mousemove",
          backgroundColor: "rgba(1,70,86,1)",
          borderColor: "rgba(0,246,255,1)",
          borderWidth: 0.5,
          textStyle: {
            fontSize: 10,
          },
        },
        series: [
          {
            type: "tree",
            hoverAnimation: true, //hover样式
            data: getdata(),
            top: "20%",
            bottom: "20%",
            left: "20%",
            right: "20%",
            layout: "radial",
            symbol: "circle",
            symbolSize: 10,
            nodePadding: 20,
            animationDurationUpdate: 750,
            expandAndCollapse: true, //子树折叠和展开的交互，默认打开
            initialTreeDepth: 2,
            roam: true, //是否开启鼠标缩放和平移漫游。scale/move/true
            focusNodeAdjacency: true,
            itemStyle: {
              borderWidth: 1,
            },
            label: {
              //标签样式
              color: "#fff",
              fontSize: 10,
              fontFamily: "SourceHanSansCN",
              //position: "top",
              //rotate: 0,
            },
            lineStyle: {
              width: 1,
              curveness: 0.5,
            },
          },
        ],
      };
      myChart.setOption(option);
    },
    // 资产运行态势图
    line() {
      var echarts = require("echarts");
      var myChart = echarts.init(document.getElementById("line"));
      var option1 = {
        backgroundColor: "#161939",
        color: ["#f0c725", "#16f892"],
        // title: {
        //   left: "center",
        //   // text: "某班某次考试各科成绩",
        //   textStyle: {
        //     color: "#FFFFFF",
        //     fontSize: "14",
        //   },
        // },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: "shadow", // 默认为直线，可选为：'line' | 'shadow'
          },
        },
        // legend: {
        //   data: [
        //     "语文",
        //     "数学",
        //     "英语",
        //     "政治",
        //     "历史",
        //     "地理",
        //     "生物",
        //     "化学",
        //     "物理",
        //   ],
        // },
        grid: {
          left: "6%",
          right: "4%",
          top: "25%",
          bottom: "3%",
          containLabel: true,
        },
        toolbox: {
          show: true,
          orient: "vertical",
          x: "right",
          y: "center",
        },
        xAxis: [
          {
            type: "category",
            boundaryGap: false,
            data: [
              "41-50",
              "51-60",
              "61-70",
              "71-80",
              "81-90",
              "91-100",
              "101-110",
              "111-120",
              "121-130",
              "131-140",
              "141-150",
            ],
            axisLine: {
              lineStyle: {
                color: "rgba(240,199,37,0.5)",
              },
            },
            axisLabel: {
              interval: 0,
              rotate: "45",
              color: "#c1cadf",
            },
          },
        ],
        yAxis: [
          {
            type: "value",
            name: "(人数)",
            show: true,
            nameTextStyle: {
              color: "#c1cadf",
              align: "right",
              lineHeight: 10,
            },
            axisLine: {
              show: true,
              lineStyle: {
                color: "rgba(240,199,37,0.5)",
              },
            },
            axisLabel: {
              interval: 0,
              color: "#c1cadf",
            },
            splitLine: {
              show: false,
              color: "",
            },
          },
        ],
        series: [
          {
            name: "语文",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(240,199,37,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(240,199,37,0.01)",
                },
              ]),
            },
            data: [3, 5, 13, 22, 26, 31, 10, 8, 2],
            barWidth: "10%",
            itemStyle: { normal: { color: "#f0c725" } },
          },
          {
            name: "数学",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [5, 12, 20, 27, 42, 11, 8, 13, 5, 1],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "英语",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 0, 8, 16, 53, 27, 13, 6, 2, 2, 1],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "物理",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 7, 14, 31, 12, 5],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "化学",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 0, 24, 40, 8, 2],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "生物",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [2, 0, 14, 31, 21, 3],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "政治",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 0, 4, 31, 39, 9],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "历史",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 0, 34, 11, 29, 19],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "历史",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [0, 0, 34, 11, 29, 19],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
          {
            name: "地理",
            type: "line",
            smooth: true,
            symbolSize: 8,
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "rgba(22,248,146,0.5)",
                },
                {
                  offset: 1,
                  color: "rgba(22,248,146,0.01)",
                },
              ]),
            },
            data: [5, 1, 19, 42, 13, 15],
            barWidth: "30%",
            itemStyle: { normal: { color: "#16f892" } },
          },
        ],
      };
      myChart.setOption(option1);
    },
    // 仪表盘
    gague() {
      var echarts = require("echarts");
      var myChart = echarts.init(document.getElementById("gague"));
      var dataArry = {
        one: 500,
        two: 300,
        three: 200,
      };
      var option2 = {
        backgroundColor: "#040042",
        tooltip: {
          formatter: "{a} <br/>{c} {b}",
        },
        series: [
          {
            name: "第一部分",
            type: "gauge",
            color: ["#f00"],
            min: 0,
            max: 8,
            splitNumber: 8,
            radius: "68%",
            center: ["15%", "55%"],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [
                  [0.4, "#203add"],
                  [1, "#0d1758"],
                ],
              },
              backgroundColor: "none",
            },
            tooltip: {
              formatter: function () {
                if (dataArry.one) {
                  return "第一部分:" + dataArry.one;
                }
              },
            },
            axisTick: {
              // 坐标轴小标记
              length: 12, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: "auto",
              },
            },
            splitLine: {
              // 分隔线
              length: 5, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle（详见lineStyle）控制线条样式
                color: "rgba(255,255,255,0.7)",
              },
            },
            axisLabel: {
              borderRadius: 1,
              color: "rgba(255,255,255,0.7)",
              padding: 1,
            },
            title: {
              // 其余属性默认使用全局文本样式，详见TEXTSTYLE
              // fontWeight: 'bolder',
              fontSize: 13,
              fontColor: "#FFF",
              color: "#FFF",
              paddingTop: 10,
              offsetCenter: [0, "90%"],
              // fontStyle: 'italic'
            },
            itemStyle: {
              color: "#1092ff",
            },
            detail: {
              shadowOffsetX: 0,
              shadowOffsetY: 0,
              // borderWidth: 1,
              textBorderColor: "#000",
              textBorderWidth: 1,
              textShadowBlur: 1,
              textShadowColor: "#fff",
              textShadowOffsetX: 0,
              textShadowOffsetY: 0,
              paddingTop: 10,
              fontFamily: "digital",
              fontSize: 20,
              width: 30,
              color: "#fff",
              rich: {},
              offsetCenter: [0, "65%"],
              formatter: function (value) {
                console.info(value);
                return value * 10 + "%";
              },
            },
            data: [
              {
                value: 5,
                name: "第一部分",
              },
            ],
          },
          {
            name: "第二部分",
            type: "gauge",
            color: ["#f00"],
            min: 0,
            max: 8,
            splitNumber: 8,
            radius: "68%",
            center: ["50%", "55%"],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [
                  [0.4, "#203add"],
                  [1, "#0d1758"],
                ],
              },
              backgroundColor: "none",
            },
            axisTick: {
              // 坐标轴小标记
              length: 12, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: "auto",
              },
            },
            tooltip: {
              formatter: function () {
                if (dataArry.two) {
                  return "第二部分:" + dataArry.two;
                }
              },
            },
            splitLine: {
              // 分隔线
              length: 5, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle（详见lineStyle）控制线条样式
                color: "rgba(255,255,255,0.7)",
              },
            },
            axisLabel: {
              borderRadius: 1,
              color: "rgba(255,255,255,0.7)",
              padding: 1,
            },
            title: {
              fontSize: 13,
              fontColor: "#FFF",
              color: "#FFF",
              paddingTop: 10,
              offsetCenter: [0, "90%"],
            },
            itemStyle: {
              color: "#1092ff",
            },
            detail: {
              shadowOffsetX: 0,
              shadowOffsetY: 0,
              textBorderColor: "#000",
              textBorderWidth: 1,
              textShadowBlur: 1,
              textShadowColor: "#fff",
              textShadowOffsetX: 0,
              textShadowOffsetY: 0,
              paddingTop: 10,
              fontFamily: "digital",
              fontSize: 20,
              width: 30,
              color: "#fff",
              rich: {},
              offsetCenter: [0, "65%"],
              formatter: function (value) {
                return value * 10 + "%";
              },
            },
            data: [
              {
                value: 3,
                name: "第二部分",
              },
            ],
          },
          {
            name: "第三部分",
            type: "gauge",
            color: ["#f00"],
            min: 0,
            max: 8,
            splitNumber: 8,
            radius: "68%",
            center: ["85%", "55%"],
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                width: 10,
                color: [
                  [0.4, "#203add"],
                  [1, "#0d1758"],
                ],
              },
              backgroundColor: "none",
            },
            axisTick: {
              // 坐标轴小标记
              length: 12, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: "auto",
              },
            },
            splitLine: {
              // 分隔线
              length: 5, // 属性length控制线长
              lineStyle: {
                // 属性lineStyle（详见lineStyle）控制线条样式
                color: "rgba(255,255,255,0.7)",
              },
            },
            tooltip: {
              formatter: function () {
                if (dataArry.three) {
                  return "第三部分:" + dataArry.three;
                }
              },
            },
            axisLabel: {
              borderRadius: 1,
              color: "rgba(255,255,255,0.7)",
              padding: 1,
            },
            title: {
              fontSize: 13,
              fontColor: "#FFF",
              color: "#FFF",
              paddingTop: 10,
              offsetCenter: [0, "90%"],
            },
            itemStyle: {
              color: "#1092ff",
            },
            detail: {
              shadowOffsetX: 0,
              shadowOffsetY: 0,
              textBorderColor: "#000",
              textBorderWidth: 1,
              textShadowBlur: 1,
              textShadowColor: "#fff",
              textShadowOffsetX: 0,
              textShadowOffsetY: 0,
              paddingTop: 10,
              fontFamily: "digital",
              fontSize: 20,
              width: 30,
              color: "#fff",
              rich: {},
              offsetCenter: [0, "65%"],
              formatter: function (value) {
                return value * 10 + "%";
              },
            },
            data: [
              {
                value: 2,
                name: "第三部分",
              },
            ],
          },
        ],
      };
      myChart.setOption(option2);
    },

    // 中国地图
    map() {
      var echarts = require("echarts");
      var myChart = echarts.init(document.getElementById("map"));
      var namedata = [
        { name: "张" },
        { name: "刘" },
        { name: "李" },
        { name: "邓" },
        { name: "熊" },
        { name: "田" },
        { name: "周" },
        { name: "赵" },
        { name: "钱" },
        { name: "孙" },
        { name: "吴" },
        { name: "郑" },
        { name: "王" },
        { name: "冯" },
        { name: "陈" },
        { name: "杨" },
        { name: "朱" },
        { name: "秦" },
        { name: "许" },
        { name: "徐" },
        { name: "何" },
        { name: "曹" },
        { name: "陶" },
        { name: "邹" },
        { name: "苏" },
        { name: "范" },
        { name: "彭" },
        { name: "鲁" },
        { name: "马" },
        { name: "方" },
        { name: "唐" },
        { name: "顾" },
      ];
      var option = null;
      var geoCoordMap = {
        上海: [119.1803, 31.2891],
        福建: [119.4543, 25.9222],
        重庆: [108.384366, 30.439702],
        北京: [116.4551, 40.2539],
        辽宁: [123.1238, 42.1216],
        河北: [114.4995, 38.1006],
        天津: [117.4219, 39.4189],
        山西: [112.3352, 37.9413],
        陕西: [109.1162, 34.2004],
        甘肃: [103.5901, 36.3043],
        宁夏: [106.3586, 38.1775],
        青海: [101.4038, 36.8207],
        新疆: [87.9236, 43.5883],
        西藏: [91.11, 29.97],
        四川: [103.9526, 30.7617],
        吉林: [125.8154, 44.2584],
        山东: [117.1582, 36.8701],
        河南: [113.4668, 34.6234],
        江苏: [118.8062, 31.9208],
        安徽: [117.29, 32.0581],
        湖北: [114.3896, 30.6628],
        浙江: [119.5313, 29.8773],
        内蒙古: [110.3467, 41.4899],
        江西: [116.0046, 28.6633],
        湖南: [113.0823, 28.2568],
        贵州: [106.6992, 26.7682],
        云南: [102.9199, 25.4663],
        广东: [113.12244, 23.009505],
        广西: [108.479, 23.1152],
        海南: [110.3893, 19.8516],
        黑龙江: [127.9688, 45.368],
        台湾: [121.4648, 25.563],
      };
      var chinaDatas = [
        {
          name: "北京",
          value: 682,
        },
        {
          name: "福建",
          value: 678.4,
        },
        {
          name: "广东",
          value: 663,
        },
        {
          name: "上海",
          value: 661,
        },

        {
          name: "河北",
          value: 548,
        },
        {
          name: "天津",
          value: 574.2,
        },
        {
          name: "山西",
          value: 596,
        },
        {
          name: "陕西",
          value: 531,
        },
        {
          name: "甘肃",
          value: 551,
        },
        {
          name: "宁夏",
          value: 566,
        },
        {
          name: "青海",
          value: 550,
        },
        {
          name: "新疆",
          value: 522.3,
        },
        {
          name: "西藏",
          value: 517,
        },
        {
          name: "四川",
          value: 622,
        },
        {
          name: "重庆",
          value: 593,
        },
        {
          name: "山东",
          value: 581,
        },
        {
          name: "河南",
          value: 530.9,
        },
        {
          name: "江苏",
          value: 627.8,
        },
        {
          name: "安徽",
          value: 618.3,
        },
        {
          name: "湖北",
          value: 571,
        },
        {
          name: "浙江",
          value: 616,
        },
        {
          name: "内蒙古",
          value: 473.4,
        },
        {
          name: "江西",
          value: 636,
        },
        {
          name: "湖南",
          value: 584,
        },
        {
          name: "贵州",
          value: 529,
        },
        {
          name: "广西",
          value: 621,
        },
        {
          name: "海南",
          value: 644,
        },
        {
          name: "辽宁",
          value: 451,
        },
        {
          name: "吉林",
          value: 518,
        },
        {
          name: "云南",
          value: 633,
        },
        {
          name: "黑龙江",
          value: 564,
        },
        {
          name: "台湾",
          value: 651,
        },
      ];
      var convertData = function (data, type) {
        /*type:1 地图参数；type:2数据参数*/
        var res = [];
        for (var i = 0; i < data.length; i++) {
          var geoCoord = geoCoordMap[data[i].name];
          if (geoCoord) {
            if (type == 2) {
              res.push({
                name: data[i].name,
                value: geoCoord.concat(data[i].value),
                username: data[i].username,
                telphone: data[i].telphone,
                address: data[i].address,
              });
            } else {
              res.push({
                name: data[i].name,
                value: geoCoord.concat(data[i].value),
              });
            }
          }
        }
        //console.log(res);
        return res;
      };
      var yData = [];
      var barData = chinaDatas;
      barData = barData.sort(function (a, b) {
        return b.value - a.value;
      });
      for (var j = 0; j < barData.length; j++) {
        if (j < 10) {
          yData.push("0" + j + barData[j].name);
        } else {
          yData.push(j + barData[j].name);
        }
      }

      var option3 = {
        backgroundColor: "#00013a",
        title: [
          {
            show: true,
            text: "2019年度销售排行",
            subtext: "单位：万辆",
            subtextStyle: {
              color: "#ffffff",
              lineHeight: 20,
            },
            textStyle: {
              color: "#fff",
              fontSize: 18,
            },
            right: 140,
            top: 20,
          },
        ],
        grid: {
          right: 10,
          top: 80,
          bottom: 50,
          width: "200",
        },
        xAxis: {
          show: false,
        },
        yAxis: {
          type: "category",
          inverse: true,
          nameGap: 16,
          axisLine: {
            show: false,
            lineStyle: {
              color: "#ddd",
            },
          },
          axisTick: {
            show: false,
            lineStyle: {
              color: "#ddd",
            },
          },
          axisLabel: {
            interval: 0,
            margin: 85,
            textStyle: {
              color: "#fff",
              align: "left",
              fontSize: 14,
            },
            rich: {
              a: {
                color: "#fff",
                backgroundColor: "#f0515e",
                width: 20,
                height: 20,
                align: "center",
                borderRadius: 2,
              },
              b: {
                color: "#fff",
                backgroundColor: "#24a5cd",
                width: 20,
                height: 20,
                align: "center",
                borderRadius: 2,
              },
            },
            formatter: function (params) {
              if (parseInt(params.slice(0, 2)) < 3) {
                return [
                  "{a|" +
                    (parseInt(params.slice(0, 2)) + 1) +
                    "}" +
                    "  " +
                    params.slice(2),
                ].join("\n");
              } else {
                return [
                  "{b|" +
                    (parseInt(params.slice(0, 2)) + 1) +
                    "}" +
                    "  " +
                    params.slice(2),
                ].join("\n");
              }
            },
          },
          data: yData,
        },
        geo: {
          map: "china",
          label: {
            show: true,
            color: "#ffffff",
            emphasis: {
              color: "white",
              show: false,
            },
          },
          roam: true, //是否允许缩放
          top: -60,
          bottom: "20%",
          left: "left",
          right: "300",
          zoom: 0.6, //默认显示级别
          scaleLimit: {
            min: 0.4,
            max: 3,
          }, //缩放级别
          itemStyle: {
            normal: {
              borderColor: "rgba(26,82,231, 1)",
              borderWidth: 1,
              areaColor: {
                type: "radial",
                x: 0.5,
                y: 0.5,
                r: 0.8,
                colorStops: [
                  {
                    offset: 0,
                    color: "rgba(14, 101, 247, .1)", // 0% 处的颜色
                  },
                  {
                    offset: 1,
                    color: "rgba(125, 183, 252, .1)", // 100% 处的颜色
                  },
                ],
                globalCoord: false, // 缺省为 false
              },
              shadowColor: "rgba(255, 255, 255, 0)",
              shadowOffsetX: -2,
              shadowOffsetY: 2,
              shadowBlur: 10,
            },
            emphasis: {
              areaColor: "rgba(249,157,51, .)",
              borderWidth: 0,
            },
          },
          //是否显示南海诸岛
          /*regions: [{
			name: "南海诸岛",
			value: 0,
			itemStyle: {
				normal: {
					opacity: 0,
					label: {
						show: false
					}
				}
			}
		}],*/
          tooltip: {
            show: false,
          },
        },
        series: [
          {
            type: "effectScatter",
            coordinateSystem: "geo",
            z: 1,
            data: [],
            symbolSize: 14,
            label: {
              normal: {
                show: true,
                formatter: function (params) {
                  return (
                    "{fline|客户：" +
                    params.data.username +
                    "  " +
                    params.data.telphone +
                    "}\n{tline|" +
                    params.data.address +
                    "}"
                  );
                },
                position: "top",
                backgroundColor: "rgba(254,174,33,.8)",
                padding: [0, 0],
                borderRadius: 3,
                lineHeight: 32,
                color: "#f7fafb",
                rich: {
                  fline: {
                    padding: [0, 10, 10, 10],
                    color: "#ffffff",
                  },
                  tline: {
                    padding: [10, 10, 0, 10],
                    color: "#ffffff",
                  },
                },
              },
              emphasis: {
                show: true,
              },
            },
            itemStyle: {
              color: "#feae21",
            },
          },
          {
            type: "effectScatter",
            coordinateSystem: "geo",
            z: 1,
            data: [],
            symbolSize: 14,
            label: {
              normal: {
                show: true,
                formatter: function (params) {
                  return (
                    "{fline|客户：" +
                    params.data.username +
                    "  " +
                    params.data.telphone +
                    "}\n{tline|" +
                    params.data.address +
                    "}"
                  );
                },
                position: "top",
                backgroundColor: "rgba(233,63,66,.9)",
                padding: [0, 0],
                borderRadius: 3,
                lineHeight: 32,
                color: "#ffffff",
                rich: {
                  fline: {
                    padding: [0, 10, 10, 10],
                    color: "#ffffff",
                  },
                  tline: {
                    padding: [10, 10, 0, 10],
                    color: "#ffffff",
                  },
                },
              },
              emphasis: {
                show: true,
              },
            },
            itemStyle: {
              color: "#e93f42",
            },
          },
          {
            type: "effectScatter",
            coordinateSystem: "geo",
            z: 1,
            data: [],
            symbolSize: 14,
            label: {
              normal: {
                show: true,
                formatter: function (params) {
                  return (
                    "{fline|客户：" +
                    params.data.username +
                    "  " +
                    params.data.telphone +
                    "}\n{tline|" +
                    params.data.address +
                    "}"
                  );
                },
                position: "top",
                backgroundColor: "rgba(8,186,236,.9)",
                padding: [0, 0],
                borderRadius: 3,
                lineHeight: 32,
                color: "#ffffff",
                rich: {
                  fline: {
                    padding: [0, 10, 10, 10],
                    color: "#ffffff",
                  },
                  tline: {
                    padding: [10, 10, 0, 10],
                    color: "#ffffff",
                  },
                },
              },
              emphasis: {
                show: true,
              },
            },
            itemStyle: {
              color: "#08baec",
            },
          },
          //地图
          {
            type: "map",
            mapType: "china",
            geoIndex: 0,
            z: 0,
            data: convertData(chinaDatas, 1),
          },
          {
            name: "barSer",
            type: "bar",
            roam: false,
            visualMap: false,
            zlevel: 2,
            barMaxWidth: 16,
            barGap: 0,
            itemStyle: {
              normal: {
                color: function (params) {
                  var colorList = [
                    {
                      colorStops: [
                        {
                          offset: 0,
                          color: "#f0515e",
                        },
                        {
                          offset: 1,
                          color: "#ef8554",
                        },
                      ],
                    },
                    {
                      colorStops: [
                        {
                          offset: 0,
                          color: "#3c38e4",
                        },
                        {
                          offset: 1,
                          color: "#24a5cd",
                        },
                      ],
                    },
                  ];
                  if (params.dataIndex < 3) {
                    return colorList[0];
                  } else {
                    return colorList[1];
                  }
                },
                barBorderRadius: [0, 15, 15, 0],
              },
            },
            label: {
              normal: {
                show: true,
                textBorderColor: "#333",
                textBorderWidth: 2,
              },
            },
            data: barData.sort((a, b) => {
              return b.value - a.value;
            }),
          },
        ],
      };
      if (option && typeof option === "object") {
        myChart.setOption(option, true);
      }
      function getTel() {
        var n = 2,
          telstr = "1";
        while (n < 12) {
          if (n < 3) {
            while (1) {
              var nums = Math.floor(Math.random() * 10);
              if (
                nums !== 0 &&
                nums !== 1 &&
                nums !== 2 &&
                nums !== 3 &&
                nums !== 4 &&
                nums !== 6
              ) {
                telstr += nums;
                break;
              }
            }
          } else if (n > 3 && n < 8) {
            telstr += "*";
          } else {
            telstr += Math.floor(Math.random() * 10);
          }
          n++;
        }
        return telstr;
      }
      function getName(type) {
        var name = "";
        var roundnum = Math.floor(Math.random() * 32);
        switch (type) {
          case 1:
            name = namedata[roundnum].name + "先生";
            break;
          case 2:
            name = namedata[roundnum].name + "女士";
            break;
          default:
            name = namedata[roundnum].name + "先生";
            break;
        }
        return name;
      }
      function getAddress(num, type) {
        var addstr = "";
        switch (type) {
          case 1:
            addstr = "在" + chinaDatas[num].name + "-保时捷4S店购买车辆";
            break;
          case 2:
            addstr = "在" + chinaDatas[num].name + "-奔驰4S店购买车辆";
            break;
          default:
            addstr = "在" + chinaDatas[num].name + "-法拉利4S店购买车辆";
            break;
        }
        return addstr;
      }
      var timer = setInterval(() => {
        //数据情况重绘，清除formatter移动效果，也可根据自身需求是否需要，删除这两行代码
        /*option.series[seriesjson[runidx].createType-1].data = [];
    myChart.setOption(option, true);*/
        var runidx = Math.floor(Math.random() * 3);
        var typeidx = Math.floor(Math.random() * 3);
        var dataidx = Math.floor(Math.random() * 32);
        var ranval = Math.floor(Math.random() * 10);
        chinaDatas[dataidx].value = chinaDatas[dataidx].value + ranval;
        var valarr = geoCoordMap[chinaDatas[dataidx].name];
        valarr.push(ranval);
        option.series[typeidx].data = [
          {
            name: "",
            username: getName(runidx),
            telphone: getTel(),
            address: getAddress(dataidx, typeidx),
            value: valarr,
          },
        ];
        option.series[4].data = option.series[4].data.sort(function (a, b) {
          return b.value - a.value;
        });
        myChart.setOption(option, true);
      }, 3000);
      myChart.setOption(option3);
    },
  },
};
</script>
<style scoped>
.home {
  width: 1920px;
  height: 1080px;
  background-color: #040039;
}
.title {
  display: flex;
  position: relative;
  height: 60px;
  width: 100%;
  align-items: center;
  justify-content: center;
  /* background-color: #040039; */
  color: #fff;
}
.title .none {
  flex: 1;
}
.titleRight {
  position: absolute;
  right: 35px;
  font-size: 25px;
}
.titleCenter {
  /* flex: 1; */
  flex: 6;
  display: flex;
  justify-content: center;
  font-size: 32px;
}
.header {
  display: flex;
  width: 100%;
  height: 60px;
  /* background-color: #040039; */
  color: #fff;
}
.header .box {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 34px;
}
.header .box:nth-child(7) {
  font-size: 15px;
}
.main1 {
  display: flex;
  height: 680px;
}
.main1 .sutuo {
  flex: 1;
  background-color: pink;
  position: relative;
}
.main1 .sutuo .tip {
  position: absolute;
  z-index: 999;
  top: 5%;
  right: 10%;
  font-size: 20px;
  color: #fff;
}
.main1 .map {
  flex: 2;
  /* background-color: red; */
  position: relative;
  height: 960px;
}

.footer {
  width: 100%;
  height: 280px;
  /* background-color: pink; */
  display: flex;
  box-sizing: border-box;
}

.footer .line {
  margin: 40px 0px 15px 15px;
  position: relative;
  box-sizing: border-box;
}
.footer .line .tip {
  position: absolute;
  top: 0;
  right: 35%;
  z-index: 9;
  color: #fff;
  font-size: 19px;
}
.footer .line .tip2 {
  position: absolute;
  z-index: 2;
  color: #fff;
  left: 12%;
  top: 5%;
  font-size: 22px;
}
.footerBox {
  flex: 1;
}

/* 图表的大小 */
#sutuo {
  width: 640px;
  height: 680px;
}
.footer #line {
  width: 100%;
  height: 100%;
}
.footer #gague {
  width: 100%;
  height: 100%;
  margin-left: 100px;
}
#map {
  position: absolute;
  /* top: 1000px; */
  width: 100%;
  height: 100%;
}
</style>
