<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <input id="input-search" type="text" class="form-control" v-model="winYear" placeholder='Year'>
    <ul>
      <!-- <template v-for="item in winners.StandingsLists" v-if="inRange(item.season)">
        <ul>
          <li> -->
            <!-- <h3>{{ item.season }}</h3> -->
            <template
              v-for="item in driverDetail"
              :class="{ 'active': item[0] }"
              :text="item">
              {{ item }}
            </template>
          <!-- </li>
        </ul>
      </template> -->
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'app',
  data () {
    return {
      msg: 'F1 Champions Collection',
      drivers: [],
      details: [],
      winners: [],
      winYear: "",
    }
  },
  computed: {
     yearFilter: function() {
       let textSearch = this.textSearch;
       return this.winners.filter(function(el) {
         return el.season.toLowerCase().indexOf(textSearch.toLowerCase()) !== -1;
       });
     },
     driverDetail: function () {
        for(let j = 0; j < this.winners.StandingsLists.length; j++){
          this.drivers.push({season: this.winners.StandingsLists[j].season, winner: this.winners.StandingsLists[j].DriverStandings[0].Driver.givenName});
        }
      console.log(this.drivers);
      return this.drivers;
     }
  },
  mounted() {
      //Get Champions
      const winner = 1;
      let winYear = this.winYear;

      axios.get("http://ergast.com/api/f1/" + this.winYear + "driverStandings/" + winner + ".json", {
        params: {
          limit: "100",
          offset: "55"
        }
      }).then(result => {
            this.winners = result.data.MRData.StandingsTable;
            this.details = this.winners.StandingsLists;
        }, error => {
            console.error(error);
        });
  },
  methods: {

    startFrom (arr, idx) {
      return arr.slice(idx);
    },

    inRange: function(year) {
      var i;
      for (i = 0; i < year.length; i++) {
        if (year >= "2005" && year <= "2015") {
          return true;
        break;
        }
      }
      return false;
    }
    
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
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
</style>
