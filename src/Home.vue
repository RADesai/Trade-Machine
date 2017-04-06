<template>
  <div class="container">
    <div class="row text-center heading">
      <div class="col-md-8 col-md-offset-2">
        <h1>NBA Trade Machine</h1>
        <hr class="orange">
      </div>
    </div>


    <div v-if="!trading" class="row text-center">
      <div class="col-md-4 col-md-offset-2 navigation">
        <h2 v-if="!teamCheck()">Select Participating Teams</h2>
        <h2 v-if="teamCheck()">Advance To Trade</h2>
      </div>
      <div v-if="teamCheck()" class="col-md-4 linking">
        <!-- <router-link :to="{ path: '/trade', params: { ${team1}${team2} } }"> -->
        <div @click="startTrade()" class="well well-sm link">
          <span class="glyphicon glyphicon-menu-right"></span>
        </div>
        <!-- </router-link> -->
      </div>
    </div>

    <div v-if="!trading" class="row text-center top">
      <div class="col-md-4 col-md-offset-2 team">
        <h4>{{ team1 }}</h4>
        <input v-model="query1" placeholder="Search for a team"><br><br>
      </div>

      <div class="col-md-4 team">
        <h4>{{ team2 }}</h4>
        <input v-model="query2" placeholder="Search for a team"><br><br>
      </div>
    </div>

    <div v-if="!trading" class="row">
      <div class="col-md-2 col-md-offset-2 text-left">
        <transition-group
          v-if="query1"
          name="staggered-fade"
          tag="ul"
          v-bind:css="false"
          v-on:before-enter="beforeEnter"
          v-on:enter="enter"
          v-on:leave="leave"
        >
          <li
            v-for="(team, index) in team1Select"
            v-bind:key="team.teamName"
            v-bind:data-index="index"
            @click="selectTeam('team1', team.teamName)"
            class="selection"
          >{{ team.teamName }}</li>
        </transition-group>
      </div>

      <div class="col-md-4 teams">
        <div class="col-md-6">
          <img v-if="team1!== 'Select Team 1'" src="http://image.ibb.co/ishqqa/basketball_3.png" alt="basketball_3">
        </div>
        <div class="col-md-6">
          <img v-if="team2!== 'Select Team 2'" src="http://image.ibb.co/jczybF/basketball_2.png" alt="basketball_2">
        </div>
      </div>

      <div v-if="query2" class="col-md-2 text-right">
        <transition-group
          name="staggered-fade"
          tag="ul"
          v-bind:css="false"
          v-on:before-enter="beforeEnter"
          v-on:enter="enter"
          v-on:leave="leave"
        >
          <li
            v-for="(team, index) in team2Select"
            v-bind:key="team.teamName"
            v-bind:data-index="index"
            @click="selectTeam('team2', team.teamName)"
            class="selection"
          >
            {{ team.teamName }}
          </li>
        </transition-group>
      </div>
    </div>

    <Trade v-if="trading" :teamOne="team1" :teamTwo="team2"></Trade>

    <div class="row">
      <div class="col-md-8 col-md-offset-2">
        <hr v-if="!trading" class="orange">
      </div>
      <hr v-if="trading" class="orange">
    </div>

    <div v-if="trading" class="row">
      <div @click="trading = false" class="col-md-4 col-md-offset-1 text-center linking">
        <div class="well well-sm link">
          <span class="glyphicon glyphicon-menu-left"></span>
        </div>
      </div>
      <div class="col-md-4 navigation">
        <h2>Re-Select Teams</h2>
      </div>
    </div>

    <small class="credits text-right">
      <div>Icons made by <a href="http://www.flaticon.com/authors/popcorns-arts" title="Popcorns Arts">Popcorns Arts</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.freepik.com" title="Freepik">Freepik</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/darius-dan" title="Darius Dan">Darius Dan</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/nikita-golubev" title="Nikita Golubev">Nikita Golubev</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/madebyoliver" title="Madebyoliver">Madebyoliver</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
    </small>
  </div>
</template>

<script>
import teams from './data/teams';
import Trade from './Trade.vue';
  export default {
    name: 'app',
    components: {
      Trade
    },
    data() {
      return {
        team1: 'Select Team 1',
        team2: 'Select Team 2',
        query1: '',
        query2: '',
        teams1: teams,
        teams2: teams,
        trading: false
      }
    },
    computed: {
      team1Select: function () {
        var vm = this
        return this.teams1.filter(function (team) {
          return team.teamName.toLowerCase().indexOf(vm.query1.toLowerCase()) !== -1 && team.teamName !== vm.team2
        });
      },
      team2Select: function () {
        var vm = this
        return this.teams2.filter(function (team) {
          return team.teamName.toLowerCase().indexOf(vm.query2.toLowerCase()) !== -1 && team.teamName !== vm.team1
        });
      }
    },
    methods: {
      selectTeam: function(team, selection) {
        this[team] = selection;
      },
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
      teamCheck: function() {
        if (this.team1 !== 'Select Team 1' && this.team2 !== 'Select Team 2') {
          return true;
        }
        return false;
      },
      startTrade: function() {
        this.trading = true;
      }
    }
  }
</script>

<style>
body {
  color: #f1f4ff;
  background-color: #000;
  font-family: 'Palanquin', sans-serif;
}

.heading {
  font-family: 'News Cycle', sans-serif;
}
.orange {
  border-color: #ED8D1F;
}

h2 {
  font-family: 'News Cycle', sans-serif;
  margin-top: 0;
}
.navigation {
  font-family: 'News Cycle', sans-serif;
}
.navigation:hover ~ .linking .well {
  border-color: #63D297;
}
.well .glyphicon-menu-right {
  transition: .3s ease-out;
}
.navigation:hover ~ .linking .well .glyphicon-menu-right {
  color: #63D297;
  transform: translateX(25px);
}

.well:hover .glyphicon-menu-right {
  color: #000;
}

.top {

}

input {
  padding: 5px;
  width: 100%;
  background-color: transparent;
  border-color: transparent;
  border-radius: 0px 0px 10px 0px;
  font-size: 1.25em;
  color: #C4CFD5;
  border-bottom-color: #000;
  transition: .3s ease-out;
  border-bottom-color: #757575;
}
input:hover {}
input:focus {
  outline: none;
  border-bottom-color: #C4CFD5;
}

.team {
  padding: 15px;
  /*border-top: 4px solid #00A2A5;*/
  transition: .3s ease-out;
}
.team:hover {
  /*box-shadow: 0px 2px 4px #00a2a5;*/
}

ul {
  padding-left: 0px;
  overflow: scroll;
  max-height: 400px;
  /*flex-wrap: wrap;*/
  /*display: flex;*/
}

li.selection {
  color: #C4CFD5;

  list-style: none;
  border: 1px #C4CFD5;
}
li.selection:hover {
  cursor: pointer;
  color: #f1f4ff;
}

.middle {
  padding: 15px;
  border-bottom: 1px solid #ef586b;
  border-top: 4px solid #ef586b;
  transition: .3s ease-out;
}
.middle:hover {
  /*box-shadow: 0px 2px 4px #ef586b;*/
  color: #ef586b;
}

.well {
  background-color: transparent;
  transition: .3s ease-out;
  font-family: 'Palanquin', sans-serif;
  color: #f1f4ff;
}
.well:hover {
  background-color: #ed8d1f;
  cursor: pointer;
  border: 1px solid #ed8d1f;
}

</style>
