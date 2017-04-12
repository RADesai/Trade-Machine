<template>
  <div class="container">
    <div v-if="!done" class="row text-center evaluation">
      <div v-if="teamOneTrades.salary.net >= 0" class="col-md-3 col-md-offset-2 text-center gain team-1">
        {{ teamOne.name }}
        <br>+{{ beautify(teamOneTrades.salary.net) }}
        <br v-if="!inTrade()"><br v-if="!inTrade()">
      </div>
      <div v-if="teamOneTrades.salary.net < 0" class="col-md-3 col-md-offset-2 text-center loss team-1">
        {{ teamOne.name }}
        <br>-{{ beautify(Math.abs(teamOneTrades.salary.net)) }}
        <br v-if="!inTrade()"><br v-if="!inTrade()">
      </div>

      <div v-if="!inTrade()" class="col-md-2 court">
        <img src="https://image.ibb.co/d3c5ak/basketball_court_1.png" alt="basketball_court_1" border="0">
      </div>
      <div v-if="inTrade()" class="col-md-2">
        {{ tradeChecker() }}
        <div v-if="valid" @click="confirmTrade" class="well well-sm valid confirm">
          <span class="glyphicon glyphicon-transfer"></span>
        </div>
        <div v-if="!valid" class="well well-sm invalid loss">
          <span class="glyphicon glyphicon-remove-circle"></span>
        </div>
      </div>

      <div v-if="teamTwoTrades.salary.net >= 0" class="col-md-3 text-center gain team-2">
        {{ teamTwo.name }}
        <br>+{{ beautify(teamTwoTrades.salary.net) }}
      </div>
      <div v-if="teamTwoTrades.salary.net < 0" class="col-md-3 text-center loss team-2">
        {{ teamTwo.name }}
        <br>-{{ beautify(Math.abs(teamTwoTrades.salary.net)) }}
      </div>
    </div>

    <div class="row text-center">
      <div class="col-md-5 col-md-offset-1 team team-1">
        <div class="col-md-6">
          <img :src="teamOne.logo">
        </div>
        <div class="col-md-6">
          <ul class="list-group">
            <li @click="tradePlayer(player)" v-for="(player, index) in team1Players" class="list-group-item player">
              {{ player.name }} - {{ player.position }}<br>
              {{ beautify(player.salary) }} - 1 Year
            </li>
          </ul>
        </div>
      </div>
      <div class="col-md-5 team team-2">
        <div class="col-md-6">
          <ul class="list-group">
            <li @click="tradePlayer(player)" v-for="(player, index) in team2Players" class="list-group-item player">
              {{ player.name }} - {{ player.position }}<br>
              {{ beautify(player.salary) }} - 2 Years
            </li>
          </ul>
        </div>
        <div class="col-md-6">
          <img :src="teamTwo.logo">
        </div>
      </div>
    </div>

    <div v-if="teamTwoTrades.players.length > 0 || teamOneTrades.players.length > 0" class="row">
      <div class="col-md-6 team-1">
        <h3>{{ teamOne.name }} Receive</h3>
        <ul class="list-group">
          <li v-for="(player, i) in teamTwoTrades.players" class="list-group-item traded team-1">
            <div class="row">
              <div class="col-md-6 name">
                <h4>{{ player.name }} - {{ player.position }}</h4>
              </div>
              <div class="col-md-5">
                <span class="stats">PPG: 20.6 APG: 3.8 RPG: 8.2<br>SPG: 0.5 BPG: 1.3 FG%: 54.2</span>
              </div>
              <div class="col-md-1">
                <span @click="keepPlayer(player, i)" class="glyphicon glyphicon-minus"></span>
              </div>
            </div>
          </li>
        </ul>
      </div>

      <div class="col-md-6 team-2">
        <h3>{{ teamTwo.name }} Receive</h3>
        <ul class="col-md-12 list-group">
          <li v-for="(player, i) in teamOneTrades.players" class="list-group-item traded team-2">
            <div class="row">
              <div class="col-md-6 name">
                <h4>{{ player.name }} - {{ player.position }}</h4>
              </div>
              <div class="col-md-5">
                <span class="stats">PPG: 22.5 APG: 6.6 RPG: 4.5<br>SPG: 1.8 BPG: 0.9 FG%: 51.7</span>
              </div>
              <div class="col-md-1">
                <span @click="keepPlayer(player, i)" class="glyphicon glyphicon-minus"></span>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>

    <div v-if="done" class="row results">
      <br>
      <div class="col-md-6 results-1">
        <div class="col-md-3 text-center playerProfile">
          <div v-if="preview1.show">
            <span class="glyphicon glyphicon-user"></span><br>
            <h5>{{ preview1.name }}</h5>
            <h6 v-for="(stat, index) in preview1.stats">{{ stat }}<br></h6>
          </div>
        </div>
        <div class="col-md-3 text-center bank">
          <!-- <span v-if="received.teamOneSalary >= 0" class="glyphicon glyphicon-piggy-bank gain"></span>
          <span v-if="received.teamOneSalary < 0" class="glyphicon glyphicon-piggy-bank loss"></span> -->
          <span class="glyphicon glyphicon-piggy-bank"></span>
          <br><h5>CAP SPACE: $724,946</h5>
        </div>
        <div class="col-md-6 text-right">
          <h3 class="team-link">{{ teamOne.name }}</h3><hr class="gold gold-1"><br>
          <span @click="previewPlayer(player, 1)" class="roster" v-for="(player, index) in team1Players">{{ player.name }}<br></span>
          <span v-if="received.teamOneSalary >= 0" class="gain">+{{ beautify(received.teamOneSalary) }}<br></span>
          <span v-if="received.teamOneSalary < 0" class="loss">-{{ beautify(Math.abs(received.teamOneSalary)) }}<br></span>
        </div>
      </div>

      <div class="col-md-6 text-left results-2">
        <div class="col-md-6">
          <h3 class="team-link">{{ teamTwo.name }}</h3><hr class="gold gold-2"><br>
          <span @click="previewPlayer(player, 2)" class="roster" v-for="(player, index) in team2Players">{{ player.name }}<br></span>
          <span v-if="received.teamTwoSalary >= 0" class="gain">+{{ beautify(received.teamTwoSalary) }}<br></span>
          <span v-if="received.teamTwoSalary < 0" class="loss">-{{ beautify(Math.abs(received.teamTwoSalary)) }}<br></span>
        </div>
        <div class="col-md-3 text-center bank">
          <!-- <span v-if="received.teamTwoSalary >= 0" class="glyphicon glyphicon-piggy-bank gain"></span>
          <span v-if="received.teamTwoSalary < 0" class="glyphicon glyphicon-piggy-bank loss"></span> -->
          <span class="glyphicon glyphicon-piggy-bank"></span>
          <br><h5>CAP SPACE: $2,978,536</h5>
        </div>
        <div class="col-md-3 text-center playerProfile">
          <div v-if="preview2.show">
            <span class="glyphicon glyphicon-user"></span><br>
            <h5>{{ preview2.name }}</h5>
            <h6 v-for="(stat, index) in preview2.stats">{{ stat }}<br></h6>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import players from './data/players'
  export default {
    name: 'app',
    components: {},
    props: [
      'teamOne',
      'teamTwo'
    ],
    data() {
      return {
        players1: players,
        players2: players,
        teamOneTrades: {
          players: [],
          salary: {
            trading: 0,
            net: 0
          }
        },
        teamTwoTrades: {
          players: [],
          salary: {
            trading: 0,
            net: 0
          }
        },
        disabled: [],
        valid: null,
        message: 'Invalid Trade',
        received: {
          'teamOne': [],
          'teamOneSalary': 0,
          'teamTwo': [],
          'teamTwoSalary': 0
        },
        done: false,
        preview1: {
          show: false,
          name: '',
          stats: []
        },
        preview2: {
          show: false,
          name: '',
          stats: []
        }
      }
    },
    computed: {
      team1Players: function () {
        var vm = this
        return this.players1.filter(function (player) {
          return player.teamName === vm.teamOne.name;
        });
      },
      team2Players: function () {
        var vm = this
        return this.players2.filter(function (player) {
          return player.teamName === vm.teamTwo.name;
        });
      }
    },
    mounted() {},
    methods: {
      beforeEnter: function (el) {
        el.style.opacity = 0
        el.style.height = 0
      },
      enter: function (el, done) {
        var delay = el.dataset.index
        setTimeout(function () {
          Velocity(
            el,
            { opacity: 1, height: '1.6em' },
            { complete: done }
          )
        }, delay)
      },
      leave: function (el, done) {
        var delay = el.dataset.index
        setTimeout(function () {
          Velocity(
            el,
            { opacity: 0, height: 0 },
            { complete: done }
          )
        }, delay)
      },
      inTrade: function() {
        return this.teamOneTrades.players.length !== 0 && this.teamTwoTrades.players.length !== 0
      },
      beautify(salary) {
        let num = salary.toString().split('');
        let niceNum = [];
        let count = 0;
        for (var i = num.length - 1; i >= 0; i--) {
          if (count === 3) {
            niceNum.push(',');
            count = 0;
          }
          niceNum.push(num[i]);
          count++;
        }
        return `$${niceNum.reverse().join('')}`;
      },
      tradePlayer: function(player) {
        this.done = false;
        this.received.teamOne = [];
        this.received.teamOneSalary = 0;
        this.received.teamTwo = [];
        this.received.teamTwoSalary = 0;
        if (player.teamName === this.teamOne.name && this.disabled.indexOf(player.name) === -1) {
          this.teamOneTrades.players.push(player);
          this.disabled.push(player.name);
          this.teamOneTrades.salary.trading += player.salary;
          this.teamOneTrades.salary.net += player.salary;
          this.teamTwoTrades.salary.net -= player.salary;
        } else if (player.teamName === this.teamTwo.name && this.disabled.indexOf(player.name) === -1) {
          this.teamTwoTrades.players.push(player)
          this.disabled.push(player.name);
          this.teamTwoTrades.salary.trading += player.salary;
          this.teamTwoTrades.salary.net += player.salary;
          this.teamOneTrades.salary.net -= player.salary;
        }
      },
      tradeChecker: function() {
        let low = Math.min(this.teamOneTrades.salary.trading, this.teamTwoTrades.salary.trading);
        let high = Math.max(this.teamOneTrades.salary.trading, this.teamTwoTrades.salary.trading);
        if (low === 0 || high === 0) {
          this.valid = null;
        } else {
          low * 1.5 >= high ? this.valid = true : this.valid = false;
          // this.valid = low * 1.5 >= high;
        }
      },
      keepPlayer: function(player, index) {
        if (player.teamName === this.teamOne.name) {
          this.teamOneTrades.players.splice(index, 1); // Find and remove player from teamOneTrades and disabled
          let keeping = null;
          for (var i = 0; i < this.disabled.length; i++) {
            let currentPlayer = this.disabled[i];
            if (currentPlayer === player.name) {
              keeping = i;
            }
          }
          this.disabled.splice(keeping, 1);
          this.teamOneTrades.salary.trading -= player.salary;
          this.teamOneTrades.salary.net -= player.salary;
          this.teamTwoTrades.salary.net += player.salary;
        } else if (player.teamName === this.teamTwo.name) {
          this.teamTwoTrades.players.splice(index, 1);
          let keeping = null;
          for (var i = 0; i < this.disabled.length; i++) {
            let currentPlayer = this.disabled[i];
            if (currentPlayer === player.name) {
              keeping = i;
            }
          }
          this.disabled.splice(keeping, 1);
          this.teamTwoTrades.salary.trading -= player.salary;
          this.teamTwoTrades.salary.net -= player.salary;
          this.teamOneTrades.salary.net += player.salary;
        }
      },
      confirmTrade: function() {
        for (var i = 0; i < this.teamOneTrades.players.length; i++) {
          let currentPlayer = this.teamOneTrades.players[i];
          currentPlayer.teamName = this.teamTwo.name;
          this.received.teamTwo.push(currentPlayer);
        }
        this.teamOneTrades.players = [];
        for (var i = 0; i < this.teamTwoTrades.players.length; i++) {
          let currentPlayer = this.teamTwoTrades.players[i];
          currentPlayer.teamName = this.teamOne.name;
          this.received.teamOne.push(currentPlayer);
        }
        this.teamTwoTrades.players = [];
        this.teamOneTrades.salary.trading = 0;
        this.received.teamOneSalary = this.teamOneTrades.salary.net;
        this.teamOneTrades.salary.net = 0;
        this.teamTwoTrades.salary.trading = 0;
        this.received.teamTwoSalary = this.teamTwoTrades.salary.net;
        this.teamTwoTrades.salary.net = 0;
        this.disabled = [];
        this.done = true;
      },
      previewPlayer: function(player, team) {
        let dummyStats = [
          ['PPG: 27.2', 'RPG: 11.2', 'APG: 7.2', 'SPG: 1.4', 'BPG: 2.0'],
          ['PPG: 14.6', 'RPG: 5.9', 'APG: 11.4', 'SPG: 2.1', 'BPG: 0.7'],
          ['PPG: 11.6', 'RPG: 8.8', 'APG: 9.1', 'SPG: 2.3', 'BPG: 0.8'],
          ['PPG: 18.1', 'RPG: 6.3', 'APG: 5.2', 'SPG: 0.7', 'BPG: 1.1'],
          ['PPG: 19.3', 'RPG: 7.8', 'APG: 3.5', 'SPG: 1.1', 'BPG: 1.6'],
          ['PPG: 31.2', 'RPG: 4.0', 'APG: 4.4', 'SPG: 1.3', 'BPG: 0.9']
        ];
        let rand = Math.floor(Math.random() * dummyStats.length);
        team === 1 ? this.preview1.show = true : this.preview2.show = true;
        team === 1 ? this.preview1.name = player.name : this.preview2.name = player.name;
        team === 1 ? this.preview1.stats = dummyStats[rand] : this.preview2.stats = dummyStats[rand];
      }
    }
  }
</script>

<style>
.evaluation {
  font-family: 'News Cycle', sans-serif;
  font-size: 1.25em;
}
.results {
  font-family: 'News Cycle', sans-serif;
  color: #C4CFD5;
}

.team-link:hover {
  cursor: pointer;
  color: #f1f4ff;
}

.results-1,
.results-2 {
  border: 1px solid #000;
  transition: .3s ease-out;
}

.results-1 .playerProfile,
.results-1 .bank,
.results-2 .playerProfile,
.results-2 .bank {
  margin-top: 20px;
  font-size: 1.6em;
  transition: .3s ease-out;
  color: #000;
}
.results-1:hover .playerProfile,
.results-1:hover .bank,
.results-2:hover .playerProfile,
.results-2:hover .bank {
  color: #C4CFD5;
}

.results-1 .gold {
  width: 65%;
  border-color: #C4CFD5;
  -moz-border-image: -moz-linear-gradient(left, #ffd877 0%, #C4CFD5 100%);
  -webkit-border-image: -webkit-linear-gradient(left, #ffd877 0%, #C4CFD5 100%);
  border-image: linear-gradient(to right, #ffd877 0%, #C4CFD5 100%);
  border-image-slice: 1;
}
.results-2 .gold {
  width: 65%;
  border-color: #C4CFD5;
  -moz-border-image: -moz-linear-gradient(left, #C4CFD5 0%, #ffd877 100%);
  -webkit-border-image: -webkit-linear-gradient(left, #C4CFD5 0%, #ffd877 100%);
  border-image: linear-gradient(to right, #C4CFD5 0%, #ffd877 100%);
  border-image-slice: 1;
}

.results-1:hover {
  -moz-border-image: -moz-linear-gradient(left, #ffd877 0%, #000 100%);
  -webkit-border-image: -webkit-linear-gradient(left, #ffd877 0%, #000 100%);
  border-image: linear-gradient(to right, #ffd877 0%, #000 100%);
  border-image-slice: 1;
}
.results-2:hover {
  -moz-border-image: -moz-linear-gradient(left, #000 0%, #C4CFD5 100%);
  -webkit-border-image: -webkit-linear-gradient(left, #000 0%, #C4CFD5 100%);
  border-image: linear-gradient(to right, #000 0%, #C4CFD5 100%);
  border-image-slice: 1;
}
.roster {
  transition: .3s ease-out;
}
.roster:hover {
  color: #f1f4ff;
  cursor: pointer;
}

.gold {
  border-color: #C4CFD5;
  width: 50%;
  transition: .3s ease-out;
  margin: 5px;
}
.gold-1 {
  float: right;
  margin-right: 0px;
}
.gold-2 {
  float: left;
  margin-left: 0px;
}

.glyphicon-transfer {
  color: #63D297;
  transition: .3s ease-out;
}

.gain {
  color: #63D297;
}
.loss {
  color: #EE3017;
}
.well.loss {
  border: 1px solid #ed8d1f;
  cursor: wait;
}
.well.loss:hover {
  border-color: #EE3017;
  background-color: transparent;
  cursor: not-allowed;
}
.well.confirm {
  border: 1px solid #63D297;
}
.well.confirm:hover {
  background-color: #63D297;
  cursor: pointer;
  border: 1px solid #63D297;
}
.well.confirm:hover .glyphicon-transfer {
  color: #000;
}
.valid {
  color: #000;
  transition: .2s ease-out;
}
.valid:hover {
  color: #63d297;
}

.traded {
  background-color: transparent;
}
.traded.team-1,
.traded.team-2 {
  border-radius: 0px;
  /*border-left: 2px solid #C4CFD5;
  border-right: 2px solid #C4CFD5;*/
}

.traded.team-1:hover,
.traded.team-2:hover {
  border-left: 1px solid #ffd877;
  border-right: 1px solid #ffd877;
}
.traded.team-1:hover .name,
.traded.team-2:hover  .name {
  color: #ffd877;
}

.name {
  transition: .3s ease-out;
}
.traded:hover .name {}
.list-group-item.traded {
  transition: .3s ease-out;
}

.team-1 {
  border-radius: 10px 0px 0px 0px;
  transition: .3s ease-out;
}
.team-2 {
  border-radius: 0px 10px 0px 0px;
  transition: .3s ease-out;
}

.list-group-item.player {
  padding: 5px;
  background-color: transparent;
  color: #C4CFD5;
  transition: .3s ease-out;
}
.list-group-item.player:hover {
  cursor: pointer;
  background-color: #C4CFD5;
  color: #000;
}

.team img {
  width: 100%;
}

span.glyphicon-minus {
  transition: .2s ease-out;
  font-size: 1.4em;
}
span.glyphicon-minus:hover {
  color: #EE3017;
  cursor: pointer;
}
</style>
