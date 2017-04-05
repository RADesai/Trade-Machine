<template>
  <div class="container">
    <div class="row">
      <div class="col-md-3">
        <br><h3>Select Players to Trade</h3><br>
      </div>
      <div class="col-md-9 text-center">
        <br><h3>Select Players to Trade</h3><br>
      </div>
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
      <div class="col-md-8 col-md-offset-2 evaluation">
        <div class="col-md-6">
          <h3>{{ teamOne }} Receive:</h3>
          <span v-for="(player, i) in teamTwoTrades.players" class="traded team-1">
            {{ player.name }}<br>
          </span>
        </div>
        <div class="col-md-6">
          <h3>{{ teamTwo }} Receive:</h3>
          <span v-for="(player, i) in teamOneTrades.players" class="traded team-2">
            {{ player.name }}<br>
          </span>
        </div>
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
import players from './players'
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
          salary: 0
        },
        teamTwoTrades: {
          players: [],
          salary: 0
        }
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
    mounted() {
      this.$on('team1', function (team) {
        console.log('Team:', team);
      });
      console.log('props:', this.props);
      console.log('params:', this.params);
      console.log('team1:', this.team1);
      console.log('team2:', this.team2);
    },
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
        if (player.teamName === this.teamOne) {
          this.teamOneTrades.players.push(player);
          this.teamOneTrades.salary += player.salary;
        } else {
          this.teamTwoTrades.players.push(player)
          this.teamTwoTrades.salary += player.salary;
        }
      },
      tradeChecker: function() {

      }
    }
  }
</script>

<style>
.traded {

}
.team-1 {
  border-top: 2px solid #00A2A5;
  transition: .3s ease-out;
}
.team-2 {
  border-top: 2px solid #ef586b;
  transition: .3s ease-out;
}
</style>
