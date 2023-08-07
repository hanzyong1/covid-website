<template>
  <div class="cases-chart"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  props: ["dates", "cases"],
  data() {
    return {
      myChart: null,
      option: {
        title: { text: "Daily Cases Chart" },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        xAxis: {
          data: [],
          axisLabel: {
            rotate: 50,
          },
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [],
            type: "bar",
            color: "#f33b4a",
          },
        ],
      },
    };
  },
  methods: {
    initChart() {
      this.myChart = echarts.init(document.querySelector(".cases-chart"));
    },

    getChartData() {
      this.option.xAxis.data = this.dates;
      this.option.series[0].data = this.cases;

      this.myChart.setOption(this.option);
    },
  },
  mounted() {
    this.initChart();
    this.getChartData();
  },
};
</script>

<style scoped>
.cases-chart {
  height: 500px;
}
</style>
