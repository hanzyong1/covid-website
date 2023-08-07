<template>
  <div v-if="dataLoaded" class="home">
    <div class="homeTop">
      <div id="overviewCard">
        <p>{{ selected }}</p>
        <p>Overview</p>
        <select v-model="selected" @change="handleClick">
          <option disabled value="">Global</option>
          <option
            v-for="country in countries"
            :value="country.country"
            :key="country.country"
          >
            {{ country.country }}
          </option>
        </select>
      </div>
      <div class="homeCard bg-red">
        Confirmed
        <p class="bold">{{ global.active | formatStr }}</p>
      </div>
      <div class="homeCard bg-green">
        Recovered
        <p class="bold">{{ global.recovered | formatStr }}</p>
      </div>
      <div class="homeCard bg-grey">
        Deaths
        <p class="bold">{{ global.deaths | formatStr }}</p>
      </div>
    </div>
    <h1 class="title">Countries</h1>
    <table>
      <tr>
        <th class="countryColumn">Country</th>
        <th>Confirmed</th>
        <th>Recovered</th>
        <th>Deaths</th>
      </tr>
      <tr
        class="country-row"
        v-for="(country, index) in countries"
        :key="country.country"
        :value="country.country"
      >
        <td
          class="country-click"
          ref="child"
          @click="handleCountryClick(index)"
        >
          {{ country.country }}
        </td>
        <td>{{ country.active | formatStr }}</td>
        <td>{{ country.recovered | formatStr }}</td>
        <td>{{ country.deaths | formatStr }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import router from "@/router";
import axios from "axios";

export default {
  data() {
    return {
      apiUrl: process.env.VUE_APP_URL,
      selected: "",
      global: {},
      countries: {},
      dataLoaded: false,
    };
  },
  methods: {
    handleClick() {
      router.push({ name: "country", params: { country: this.selected } });
    },

    handleCountryClick(index) {
      this.selected = this.$refs.child[index].innerText;
      this.handleClick();
    },

    async getGlobalData() {
      const globalResponse = await axios({
        method: "get",
        url: `${this.apiUrl}all`,
      });
      this.global = globalResponse.data;
    },

    async getCountriesData() {
      const countriesResponse = await axios({
        method: "get",
        url: `${this.apiUrl}countries`,
      });
      this.countries = countriesResponse.data;
    },
  },

  async created() {
    try {
      await this.getGlobalData();
      await this.getCountriesData();
      this.dataLoaded = true;
    } catch (error) {
      console.log(error);
    }
  },
};
</script>

<style scoped>
.home {
  width: 90%;
  margin: auto;
}

.homeTop {
  display: flex;
  justify-content: center;
  column-gap: 10px;
  border: 1px solid black;
  flex-wrap: wrap;
}

.homeCard {
  border: 1px solid #bbb;
  padding: 10px 80px;
  margin: 20px 0;
  border-radius: 8px;
}

#overviewCard {
  border: 1px solid #bbb;
  padding: 10px 20px;
  margin: 20px 0;
  border-radius: 8px;
}

.title {
  text-align: left;
}

table {
  width: 100%;
  border-collapse: collapse;
}

table,
th,
td {
  border: 1px solid;
  padding: 5px 0;
}

.countryColumn {
  width: 20%;
}

.country-row:hover {
  background-color: aquamarine;
}

.country-click:hover {
  cursor: pointer;
  text-decoration: underline;
}
</style>
