<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <input id="input-search" type="text" class="form-control" v-model="textSearch" placeholder='Year'>
    <ul>
      <template v-for="item in startFrom(yearFilter, 0)">
        <li>{{ item.season }}</li>
      </template>
    </ul>
    <ul>
      <template v-for="item in winners.StandingsLists" v-if="inRange(item.season)">
        <li>{{ item.season }}</li>
        <br />
        <br />
      </template>
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
      seasons: [],
      winners: [],
      textSearch: "",
      winYear: "",
    }
  },
  computed: {
     yearFilter: function() {
       let textSearch = this.textSearch;
       return this.seasons.filter(function(el) {
         return el.season.toLowerCase().indexOf(textSearch.toLowerCase()) !== -1;
       });
     },
     winFilter: function() {
       let textSearch = this.textSearch;
       return this.winners.filter(function(el) {
         return el.season.toLowerCase().indexOf(textSearch.toLowerCase()) !== -1;
       });
     }
  },
  mounted() {
    // Get Seasons
      axios.get("http://ergast.com/api/f1/seasons.json", {
        params: {
          limit: "100",
          offset: "55"
        }
      }).then(result => {
          this.seasons = result.data.MRData.SeasonTable.Seasons;
      }, error => {
          console.error(error);
      });

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
        console.log(year);
        if (year >= "2005" && year <= "2015") {
          return "Yes";
        break;
        }
      }

      return "";
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
