<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <div>
       <!-- Initial Loop Render with Template if the season falls in "inRange" outcome -->
        <template
          v-for="(item, index) in driverDetail"
          v-if="inRange(item.season)"
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
                      <b-container class="season-content">
                        <b-row>
                              <!-- That's right, I literally went and found images for all the drivers. Please mind the Jensen Button image as it is wonkey :/ -->
                              <b-col><img class="driver-img" :src="`/img/${item.winnerCode}.jpg`" /></b-col>
                              <b-col>
                                <h4>Details:</h4> 
                                <ul>
                                  <li><span>Driver Name:</span><br /><b>{{ item.winnerDetail.givenName }} {{ item.winnerDetail.familyName }}</b></li><br>
                                  <li><span>Date-of-Birth:</span><br /><b>{{ item.winnerDetail.dateOfBirth }}</b></li><br>
                                  <li><span>Nationality:</span><br /><b>{{ item.winnerDetail.nationality }}</b></li>
                                </ul>
                              </b-col>
                              <b-col>
                                <h4>Additional Info:</h4> 
                                <ul>
                                  <li><span>Driver ID:</span><br /><b>"{{ item.winnerDetail.driverId }}"</b></li><br>
                                  <li><span>Driver Number:</span><br /><b>{{ item.winnerDetail.permanentNumber }}</b></li><br>
                                  <li><span>Driver Code:</span><br /><b>{{ item.winnerCode }}</b></li><br>
                                  <li><span>Wiki:</span><br /><b><a :href=item.winnerDetail.url target="_blank">{{ item.winnerDetail.givenName }} {{ item.winnerDetail.familyName }}</a></b></li>
                                </ul>
                              </b-col>
                          </b-row>
                        <b-row>
                          <b-col>
                            <!-- Get Rounds with onclick as to not load all rounds on initial render - intention here was to save network data -->
                            <b-btn :class="'btn btn-outline-success btn-sm'" v-on:click="getRounds(item.season)" v-b-toggle="'inner-collapse-' + index" >Season Rounds</b-btn>
                            <b-collapse :id="'inner-collapse-' + index">
                              <b-card-body>
                                <b-container >
                                  <b-row>
                                    <!-- Rounds in Season Loop Render with Template -->
                                    <template v-for="itemRound in rounds">
                                      <b-list-group class="season-rounds">
                                        <b-list-group-item> 
                                          <div class="round-name">
                                            <b>{{ itemRound.raceName }}</b>
                                          </div>
                                          <div class="round-circuit">
                                            <span> Round: 
                                              <b>{{ itemRound.round }}</b> | Circuit: <b>{{ itemRound.Circuit.circuitName }}</b>
                                            </span>
                                          </div>
                                          <div class="round-detail">
                                            <!-- Results in Rounds Loop Render with Template -->
                                            <template v-for="itemRes in itemRound.Results">
                                              <b-container :class="{ 's-r-winner': item.winnerCode === itemRes.Driver.code }">
                                                <b-row>
                                                  <b-col>
                                                    <span class="round-winner">Round Winner:<br /><b>{{ itemRes.Driver.givenName }} {{ itemRes.Driver.familyName }}</b></span><br />
                                                  </b-col>
                                                </b-row>
                                                <b-row>
                                                  <b-col>
                                                    <span> Constructor: <b>{{ itemRes.Constructor.name }}</b></span><br />
                                                    <span> Nationality: <b>{{ itemRes.Constructor.nationality }}</b></span><br />
                                                    <span> Wiki: <b><a :href=itemRes.Constructor.url target="_blank">{{ itemRes.Constructor.url }}</a></b></span><br />
                                                  </b-col>
                                                  <b-col>
                                                    <span> Laps: <b>{{ itemRes.laps }}</b></span><br />
                                                    <span> Fastest Lap: <b>{{ itemRes.FastestLap.lap }}</b></span><br />
                                                    <span> Average Speed (KPH): <b>{{ itemRes.FastestLap.AverageSpeed.speed }}</b></span><br />
                                                  </b-col>
                                                </b-row>
                                              </b-container>
                                            </template>
                                          </div>
                                        </b-list-group-item>
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
// App Start
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
  // Pushing specific detail into array for ease of access
    driverDetail: function () {
      for(let j = 0; j < this.details.length; j++){
        this.drivers.push({
          season:       this.details[j].season, 
          winner:       this.details[j].DriverStandings[0].Driver.givenName, 
          winnerDetail: this.details[j].DriverStandings[0].Driver,
          winnerCode:   this.details[j].DriverStandings[0].Driver.code
        });
      }
    return this.drivers;
    }
  },
  mounted() {
      // Getting all Champions/Seasons from API starting at 2005
      axios.get("http://ergast.com/api/f1/driverStandings/1.json", {
        params: {
          limit: "100",
          offset: "55"
        }
      }).then(result => {
      // Adding results to two arrays for ease of access
          this.winners = result.data.MRData.StandingsTable;
          this.details = this.winners.StandingsLists;
      }, error => {
          console.error(error);
      });
  },
  methods: {
    // Getting all rounds for Season based on Season parameter
    getRounds: function(season) {
      axios.get("http://ergast.com/api/f1/" + season + "/results/1.json").then(result => {
      // Adding results to two arrays for ease of access
          this.season = result.data.MRData;
          this.rounds = this.season.RaceTable.Races;
      }, error => {
          console.error(error);
      });
    },
    // Limit amount of Seasons being render to 2005-2015 decade
    inRange: function(year) {
      for (let i = 0; i < year.length; i++) {
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

    <!-- Insert SCSS -->
<style src="../css/app.scss" lang="scss"></style>
