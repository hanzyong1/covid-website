<template>
  <div v-if="dataLoaded">
    <CasesChart :dates="dates" :cases="cases" />
    <RecoveredChart :dates="dates" :recovered="recovered" />
    <DeathsChart :dates="dates" :deaths="deaths" />
  </div>
</template>

<script>
import axios from "axios";
import parse from "date-fns/parse";
import { format } from "date-fns";
import DeathsChart from "./DeathsChart.vue";
import CasesChart from "./CasesChart.vue";
import RecoveredChart from "./RecoveredChart.vue";

export default {
  components: { CasesChart, RecoveredChart, DeathsChart },
  data() {
    return {
      dataLoaded: false,
      apiUrl: process.env.VUE_APP_URL,
      country: this.$route.params.country,
      unformattedDates: [],
      dates: [],
      cases: [],
      deaths: [],
      recovered: [],
    };
  },
  methods: {
    formatDate(value) {
      const parsed = parse(value, "M/d/yy", new Date());
      return format(parsed, "dd MMM");
    },

    // get country historical data
    async getChartData() {
      const response = await axios({
        method: "get",
        url: `${this.apiUrl}historical/${this.country}`,
      });

      this.unformattedDates = Object.keys(response.data.timeline.cases);
      this.cases = Object.values(response.data.timeline.cases);
      this.deaths = Object.values(response.data.timeline.deaths);
      this.recovered = Object.values(response.data.timeline.recovered);

      this.unformattedDates.map((date) => {
        this.dates.push(this.formatDate(date));
      });

      this.dataLoaded = true;
    },
  },
  mounted() {
    this.getChartData();
  },
};
</script>
