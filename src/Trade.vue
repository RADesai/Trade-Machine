<template>
  <div class="container">
    <div class="row text-center evaluation">
      <div v-if="teamOneTrades.salary.net >= 0" class="col-md-4 gain">
        {{ teamOne }}
        <br>+{{ beautify(teamOneTrades.salary.net) }}
      </div>
      <div v-if="teamOneTrades.salary.net < 0" class="col-md-4 loss">
        {{ teamOne }}
        <br>-{{ beautify(Math.abs(teamOneTrades.salary.net)) }}
      </div>

      <div class="col-md-4">
        {{ tradeChecker() }}
        <div v-if="valid" class="valid">
          Make Trade<br>
          <div class="well well-sm confirm">
            <span class="glyphicon glyphicon-transfer"></span>
          </div>
        </div>
        <div v-if="!valid" class="invalid">
          Bad Trade
        </div>
      </div>

      <div v-if="teamTwoTrades.salary.net >= 0" class="col-md-4 gain">
        {{ teamTwo }}
        <br>+{{ beautify(teamTwoTrades.salary.net) }}
      </div>
      <div v-if="teamTwoTrades.salary.net < 0" class="col-md-4 loss">
        {{ teamTwo }}
        <br>-{{ beautify(Math.abs(teamTwoTrades.salary.net)) }}
      </div>
    </div>

    <div class="row text-center top">
      <div class="col-md-6 team team-1">
        <div class="col-md-6">
          <h4>{{ teamOne }}</h4><br>
          <img src="http://image.ibb.co/ishqqa/basketball_3.png" alt="basketball_3" border="0">
        </div>
        <div class="col-md-6">
          <ul class="list-group">
            <li @click="tradePlayer(player)" v-for="(player, index) in team1Players" class="list-group-item player">
              {{ player.name }} - {{ player.position }}<br>
              {{ beautify(player.salary) }}
            </li>
          </ul>
        </div>
      </div>
      <div class="col-md-6 team team-2">
        <div class="col-md-6">
          <ul class="list-group">
            <li @click="tradePlayer(player)" v-for="(player, index) in team2Players" class="list-group-item player">
              {{ player.name }} - {{ player.position }}<br>
              {{ beautify(player.salary) }}
            </li>
          </ul>
        </div>
        <div class="col-md-6">
          <h4>{{ teamTwo }}</h4><br>
          <img src="http://image.ibb.co/jczybF/basketball_2.png" alt="basketball_2" border="0">
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col-md-6">
        <h3>{{ teamOne }} Receive</h3>
        <ul class="list-group">
          <li v-for="(player, i) in teamTwoTrades.players" class="list-group-item traded team-1">
            <div class="row">
              <div class="col-md-6">
                <span class="name">{{ player.name }} - {{ player.position }}</span>
              </div>
              <div class="col-md-6">
                <span class="stats">PPG: 20.6 APG: 3.8 RPG: 8.2<br>SPG: 0.5 BPG: 1.3 FG%: 54.2</span>
              </div>
            </div>
          </li>
        </ul>
      </div>

      <div class="col-md-6">
        <h3>{{ teamTwo }} Receive</h3>
        <ul class="list-group">
          <li v-for="(player, i) in teamOneTrades.players" class="list-group-item traded team-2">
            <div class="row">
              <div class="col-md-6">
                <span class="name">{{ player.name }} - {{ player.position }}</span>
              </div>
              <div class="col-md-6">
                <span class="stats">PPG: 22.5 APG: 6.6 RPG: 4.5<br>SPG: 1.8 BPG: 0.9 FG%: 51.7</span>
              </div>
            </div>
          </li>
        </ul>
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
        valid: null
      }
    },
    computed: {
      team1Players: function () {
        var vm = this
        return this.players1.filter(function (player) {
          return player.teamName === vm.teamOne;
        });
      },
      team2Players: function () {
        var vm = this
        return this.players2.filter(function (player) {
          return player.teamName === vm.teamTwo;
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
        console.log('Trading:', player.name);
        if (player.teamName === this.teamOne) {
          this.teamOneTrades.players.push(player);
          this.teamOneTrades.salary.trading += player.salary;
          this.teamOneTrades.salary.net += player.salary;
          this.teamTwoTrades.salary.net -= player.salary;
        } else {
          this.teamTwoTrades.players.push(player)
          this.teamTwoTrades.salary.trading += player.salary;
          this.teamTwoTrades.salary.net += player.salary;
          this.teamOneTrades.salary.net -= player.salary;
        }
      },
      tradeChecker: function() {
        let low = Math.min(this.teamOneTrades.salary.trading, this.teamTwoTrades.salary.trading);
        let high = Math.max(this.teamOneTrades.salary.trading, this.teamTwoTrades.salary.trading);
        console.log('high, low:', high, low);
        console.log("% Diff:", (high / low) * 100);
        if (low === 0 || high === 0) {
          this.valid = null;
        } else {
          low * 1.5 >= high ? this.valid = true : this.valid = false;
          // this.valid = low * 1.5 >= high;
        }
      }
    }
  }
</script>

<style>
.evaluation {
  font-family: 'News Cycle', sans-serif;
}

/*.math {
  border: 1px solid #ed8d1f;
  border-radius: 2px 2px 4px 4px;
}*/

.glyphicon-transfer {
  color: #63D297;
  transition: .3s ease-out;
}
.glyphicon-transfer:hover {
  /*color: #000;*/
  transform: rotateY(360deg);
}

.gain {
  color: #63D297;
}
.loss {
  color: #EE3017;
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
  transition: .3s ease-out;
}
.valid:hover {
  color: #63d297;
}
.invalid {

}
.stats {
  color: #000;
  transition: .3s ease-out;
  /*border-color: #000;*/
}
.stats .blend {
  border-color: #000;
  transition: .3s ease-out;
}

.traded {
  background-color: transparent;
}
.traded:hover {
  color: #ed8d1f;
}
.traded:hover .stats {
  color: #ed8d1f;
}
.traded:hover .stats .blend {
  border-color: #ed8d1f;
}
.team-1 {
  border-top: 2px solid #00A2A5;
  transition: .3s ease-out;
}
.team-2 {
  border-top: 2px solid #ef586b;
  transition: .3s ease-out;
}
.team-2:hover {
  /*box-shadow: 0px 2px 4px #ef586b;*/
}

.list-group-item.player {
  padding: 5px;
  background-color: transparent;
  transition: .3s ease-out;
}
.list-group-item.player:hover {
  cursor: pointer;
  background-color: #f1f4ff;
  color: #000;
}
</style>
