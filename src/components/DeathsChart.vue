<template>
  <div class="deaths-chart"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  props: ["dates", "deaths"],
  data() {
    return {
      myChart: null,
      option: {
        title: { text: "Daily Deaths Chart" },
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
            color: "black",
          },
        ],
      },
    };
  },
  methods: {
    initChart() {
      this.myChart = echarts.init(document.querySelector(".deaths-chart"));
    },

    getChartData() {
      this.option.xAxis.data = this.dates;
      this.option.series[0].data = this.deaths;

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
.deaths-chart {
  height: 500px;
}
</style>
