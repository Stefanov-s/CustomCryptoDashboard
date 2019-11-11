
<script>
import Vue from 'vue';
import axios from "axios";
import Trend from 'vuetrend'
Vue.use(Trend)
export default {
  name: "Dashboard",
  data() {
    return {
      results: [],
      loading: true,
      errored: false,
      window: {
        width: screen.width,
        height: screen.height
      }
    };
  },
  mounted() {
    axios
      .get("https://coinranking1.p.rapidapi.com/coins", {
        method: "GET",
        headers: {
          "x-rapidapi-host": "coinranking1.p.rapidapi.com",
          "x-rapidapi-key": "bc3fffb2b0mshf547687387b27ccp1b6abejsna5827de3dd1f"
        }
      })
      .then(response => {
        this.results = response.data.data.coins.sort((c, n) => n.price - c.price).slice(0, 4).map(el => {
          return {title: el.name, value: el.price, id: el.id, history: el.history, all_high: el.allTimeHigh.price, change: el.change, marketCap: el.marketCap, volume: el.volume}
        })
      })
      .catch(() => {
        this.errored = true;
      })
      .finally(() => (this.loading = false));
  },
  created() {
    window.addEventListener('resize', this.handleResize)
    this.handleResize();
  },
  destroyed() {
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    handleResize() {
      this.window.width = window.innerWidth;
      this.window.height = window.innerHeight;
    }
  }
};
</script>
<template>
  <div id="app">
    <h1>Crypto Dashboard</h1>

    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>
    <section v-else >
      
      <div v-if="loading">Loading...</div>
      <div v-else class="dashboard">
        <div v-for="item in results" v-bind:key="item.id" class="dashboard-item">
          <div class="info">
            <h1>{{item.title}}</h1>
            <h1>${{Number(item.value).toFixed(2)}}</h1>
          </div>
          <trend :data="item.history.map(el => Number(el))"
          :gradient="['#664122', 'faec9ac5']"
          :height="window.height / 4"
          :padding="10"
          :radius="100"
          auto-draw
          smooth
          class="trend">
          </trend>
          <div class="metrics">
            <div class="metric metrics-market-cap">
              <h3>Market Cap</h3>
              <span>{{item.marketCap}}</span>
            </div>
            <div class="metric metrics-volume">
              <h3> 24 hr. Volume </h3>
              <span>{{item.volume}}</span>
            </div>
            <div class="metric metrics-all_time_high">
              <h3>All time high</h3>
              <span>{{Number(item.all_high).toFixed(2)}}</span>
            </div>
          </div>
        </div>


      </div>
    </section>
  </div>
</template>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.dashboard{
  display: grid;
  grid-template-columns: repeat(4, 25%);
  grid-template-rows: repeat(3, 33%);
  margin: 0 auto;
  width: 80%;
  height: 80vh;
  background-color: #fdf3e55b;
  border-radius: 5px;
}
.trend{
  grid-row: 2;
  height: 33%;
  background-color: #ffffff77;
}

.dashboard h1 {
  color: #8d5f50;
}
.metrics {
  height: 42%;
  display: flex;
  flex-direction: column;
  color: #8d5f50;
}
.info {
  height: 25%;;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.metric {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 0.9em;
  padding: 1%;
  color: #8d5f50;
}

.metric span{
  padding: 2%
}

.dashboard-item {
  display: flex;
  flex-direction: column;
  grid-row: 1 / 4;
}

@media only screen and (max-width: 600px) {
  .dashboard {
    display: flex;
    flex-direction: column;
    height: 100%;
    padding-bottom: 5%;
  }
  .dashboard .dashboard-item:not(:last-child) {
    border-bottom: 2px solid #8d5f50;
    padding-bottom: 5%;
  }
}
</style>
