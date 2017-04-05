<template>
  <div class="container">

    <div class="row text-center">
      <!-- <div class="col-md-3">
        <br><h3>Select Players to Trade</h3><br>
      </div> -->

      <!-- <div class="col-md-9 text-center"> -->

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
            <div class="well well-sm link">
              <span class="glyphicon glyphicon-resize-horizontal"></span>
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
      <!-- </div> -->
    </div>

    <div class="row text-center top">
      <div class="col-md-6 team team-1">
        <h4>{{ teamOne }}</h4>
      </div>

      <div class="col-md-6 team team-2">
        <h4>{{ teamTwo }}</h4>
      </div>
    </div>

    <div class="row text-center top">
      <div class="col-md-6">
        <div @click="tradePlayer(player)" v-for="(player, index) in team1Players" class="player">
          {{ player.name }} - {{ player.position }}<br>
          {{ beautify(player.salary) }}
        </div>
      </div>

      <div class="col-md-6">
        <div @click="tradePlayer(player)" v-for="(player, index) in team2Players" class="player">
          {{ player.name }} - {{ player. position }}<br>
          {{ beautify(player.salary) }}
        </div>
      </div>
    </div>

    <div class="row">
        <div class="col-md-6">
          <h3>{{ teamOne }} Receive:</h3>
          <span v-for="(player, i) in teamTwoTrades.players" class="traded team-1">
            <div class="col-md-6">
              <h3>{{ player.name }} - {{ player.position }}</h3><hr class="orange">
            </div>
            <div class="col-md-6">
              <div class="stats">
                <h3>PPG: 22.5 APG: 6.6</h3><hr class="blend">
              </div>
            </div>
          </span>
        </div>

        <div class="col-md-6">
          <h3>{{ teamTwo }} Receive:</h3>
          <span v-for="(player, i) in teamOneTrades.players" class="row traded team-2">
            <div class="col-md-6">
              <h3>{{ player.name }} - {{ player.position }}</h3><hr class="orange">
            </div>
            <div class="col-md-6">
              <div class="stats">
                <h4>PPG: 22.5 APG: 6.6</h4><hr class="blend">
              </div>
            </div>
          </span>
        </div>
    </div>


    <!-- <div class="row text-center">
      <div class="col-md-4 col-md-offset-2">
        <div class="well well-sm link">
          <span class="glyphicon glyphicon-menu-right"></span>
        </div>
      </div>
    </div> -->

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
          this.valid = false;
        } else {
          low * 1.5 >= high ? this.valid = true : this.valid = false;
          // this.valid = low * 1.5 >= high;
        }
      }
    }
  }
</script>

<style>
.math {
  border: 1px solid #ed8d1f;
  border-radius: 2px 2px 4px 4px;
}

.glyphicon-resize-horizontal {
  color: #63D297;
  transition: .3s ease-out;
}
.glyphicon-resize-horizontal:hover {
  transform: rotateY(360deg);
}

.gain {
  color: #63D297;
}
.loss {
  color: #EE3017;
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
  /*color: #000;*/
  transition: .3s ease-out;
  /*border-color: #000;*/
}
.stats .blend {
  border-color: #000;
  transition: .3s ease-out;
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
  /*box-shadow: 0px 2px 4px #00a2a5;*/
  color: #ef586b;
}
</style>
