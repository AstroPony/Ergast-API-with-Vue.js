<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <input id="input-search" type="text" class="form-control" placeholder='Year'>
    <div>
        <template
          v-for="(item, index) in driverDetail"
          v-if="inRange(item.season)"
          :class="{ 'active': item[0] }"
          :text="item">
          
          <b-container >
            <b-row>
              <div role="tablist" class="season-acc">
                <b-card no-body class="mb-1">
                  <b-card-header header-tag="header" class="p-1" role="tab">
                    <b-btn block href="#" v-b-toggle="'collapse-' + index" variant="info">
                      <span>Season: </span>
                      <b>{{ item.season }}</b>
                      <span> - <span>Winner:</span> 
                      <b>{{ item.winnerDetail.familyName }}</b>, {{ item.winner }}</span>
                    </b-btn>
                  </b-card-header>
                  <b-collapse :id="'collapse-' + index" accordion="my-accordion" role="tabpanel">
                    <b-card-body>
                      <b-container >
                        <b-row>
                              <b-col>3 of 3</b-col>
                              <b-col>
                                <h4>Details:</h4> 
                                <ul>
                                  <li>Driver Name:<br /><b>{{ item.winnerDetail.givenName }} {{ item.winnerDetail.familyName }}</b></li><br>
                                  <li>Date-of-Birth:<br /><b>{{ item.winnerDetail.dateOfBirth }}</b></li><br>
                                  <li>Nationality:<br /><b>{{ item.winnerDetail.nationality }}</b></li>
                                </ul>
                              </b-col>
                              <b-col>
                                <h4>Additional Info:</h4> 
                                <ul>
                                  <li>Driver ID:<br /><b>"{{ item.winnerDetail.driverId }}"</b></li><br>
                                  <li>Driver Number:<br /><b>{{ item.winnerDetail.permanentNumber }}</b></li><br>
                                  <li>Driver Code:<br /><b>{{ item.winnerDetail.code }}</b></li><br>
                                  <li>Wiki:<br /><b><a :href=item.winnerDetail.url target="_blank">{{ item.winnerDetail.givenName }} {{ item.winnerDetail.familyName }}</a></b></li>
                                </ul>
                              </b-col>
                          </b-row>
                        <b-row>
                          <b-col>
                            <b-btn id="getMore" class="btn btn-outline-success btn-sm" v-on:click="getRounds(item.season)" v-b-toggle="'inner-collapse-' + index" >Season Rounds</b-btn>
                            <b-collapse :id="'inner-collapse-' + index">
                              <b-card-body>
                                <b-container >
                                  <b-row>
                                    <template v-for="item in rounds">
                                      <b-list-group>
                                        <b-list-group-item active>{{ item.round }} {{ item.raceName }} {{ item.circuitName }} {{ item.round }} {{ item.round }}</b-list-group-item>
                                      </b-list-group>
                                    </template>
                                  </b-row>
                                </b-container>
                              </b-card-body>
                            </b-collapse>
                          </b-col>
                        </b-row>
                      </b-container>
                    </b-card-body>
                  </b-collapse>
                </b-card>
              </div>
          </b-row>
      </b-container>
      </template>
      
    </div>
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
      season: [],
      rounds: []
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
        for(let j = 0; j < this.details.length; j++){
          this.drivers.push({
            season:       this.details[j].season, 
            winner:       this.details[j].DriverStandings[0].Driver.givenName, 
            winnerDetail: this.details[j].DriverStandings[0].Driver
          });
        }
      return this.drivers;
     }
  },
  mounted() {
      //Get Champions

      axios.get("http://ergast.com/api/f1/driverStandings/1.json", {
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
    getRounds(season) {
      // axios.get("http://ergast.com/api/f1/" + season + ".json").then(result => {
      axios.get("http://ergast.com/api/f1/" + season + "/results/1.json").then(result => {
          this.season = result.data.MRData;
          this.rounds = this.season.RaceTable.Races;
          console.log(this.rounds);
      }, error => {
          console.error(error);
      }); 

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

.season-acc {
  width: 100%;
}
</style>
