<template>
  <div class="recovered-chart"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  props: ["dates", "recovered"],
  data() {
    return {
      myChart: null,
      option: {
        title: { text: "Daily Recovered Chart" },
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
            color: "rgb(36, 219, 51)",
          },
        ],
      },
    };
  },
  methods: {
    initChart() {
      this.myChart = echarts.init(document.querySelector(".recovered-chart"));
    },

    getChartData() {
      this.option.xAxis.data = this.dates;
      this.option.series[0].data = this.recovered;

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
.recovered-chart {
  height: 500px;
}
</style>
