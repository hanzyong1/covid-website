<template>
  <div v-if="dataLoaded" class="main">
    <div>
      <div class="top">
        <div class="topLeft">
          <div class="overview">
            <p class="bold">{{ data.country }} Overview</p>
            <div class="overviewCard">
              <div class="card bg-red">
                <div class="bold">{{ data.active | formatStr }}</div>
                <p>Confirmed</p>
              </div>
              <div class="card bg-green">
                <div class="bold">{{ data.recovered | formatStr }}</div>
                <p>Recovered</p>
              </div>
              <div class="card bg-grey">
                <div class="bold">{{ data.deaths | formatStr }}</div>
                <p>Deaths</p>
              </div>
            </div>
          </div>
        </div>
        <div class="card">
          Fatality Rate
          <p class="bold">{{ fatalityRate }}</p>
        </div>
        <div class="card">
          Recovery Rate
          <p class="bold">{{ recoveryRate }}</p>
        </div>
      </div>
      <div class="bottom">
        <div class="countryCard">
          <p>Critical Cases treated in ICU</p>
          <p class="bold">{{ data.critical | formatStr }}</p>
          <p>
            <span class="bold">{{ criticalCases }}</span> of total cases
          </p>
        </div>
        <div class="countryCard">
          <p>Daily Cases Receiving Treatment</p>
          <p class="bold">{{ receivingTreatment | formatStr }}</p>
          <p>
            <span class="bold">{{ treatmentCases }}</span> of total cases
          </p>
        </div>
        <div class="countryCard">
          <p>Confirmed Cases</p>
          <p class="bold">{{ data.casesPerOneMillion | formatStr }}</p>
          <p>Per Million Population</p>
        </div>
      </div>
    </div>
    <AppChart />
  </div>
  <div v-else><h1>Loading...</h1></div>
</template>

<script>
import axios from "axios";
import AppChart from "../components/AppChart.vue";

export default {
  components: { AppChart },
  data() {
    return {
      apiUrl: process.env.VUE_APP_URL,
      country: this.$route.params.country,
      data: {},
      fatalityRate: "",
      recoveryRate: "",
      criticalCases: "",
      receivingTreatment: "",
      treatmentCases: "",
      totalCases: "",
      dataLoaded: false,
    };
  },
  methods: {
    async getCountryData() {
      const response = await axios({
        method: "get",
        url: `${this.apiUrl}countries/${this.country}`,
      });
      this.data = response.data;
      this.totalCases = response.data.cases;
      this.fatalityRate =
        ((response.data.deaths / response.data.cases) * 100).toFixed(2) + "%";
      this.recoveryRate =
        ((response.data.recovered / response.data.cases) * 100).toFixed(2) +
        "%";
      this.criticalCases =
        ((response.data.critical / response.data.cases) * 100).toFixed(2) + "%";
    },

    async getVaccineData() {
      const response = await axios({
        method: "get",
        url: `${this.apiUrl}vaccine/coverage/countries/${this.country}`,
      });
      this.receivingTreatment =
        Object.values(response.data.timeline).at(-1) -
        Object.values(response.data.timeline).at(-2);
      this.treatmentCases =
        ((this.receivingTreatment / this.totalCases) * 100).toFixed(2) + "%";
    },
  },

  async mounted() {
    try {
      await this.getCountryData();
      await this.getVaccineData();
      this.dataLoaded = true;
    } catch (error) {
      console.log(error);
    }
  },
};
</script>

<style scope>
.main {
  width: 90%;
  margin: auto;
}

.top {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 10px;
  flex-wrap: wrap;
}
.topLeft {
  display: flex;
  flex-wrap: wrap;
}

.overview {
  display: flex;
  justify-content: center;
  flex-direction: column;
  text-align: start;
  border: 1px solid;
  padding: 10px 20px;
  border-radius: 8px;
  flex-wrap: wrap;
}

.overviewCard {
  display: flex;
  gap: 10px;
  text-align: center;
  flex-wrap: wrap;
}

.bottom {
  display: flex;
  justify-content: center;
  column-gap: 30px;
  margin-bottom: 10px;
  flex-wrap: wrap;
}

.card {
  border: 1px solid black;
  padding: 15px 60px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-radius: 8px;
}

.countryCard {
  padding: 10px 60px;
  border: 1px solid black;
  border-radius: 8px;
}
</style>
