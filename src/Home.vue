<template>
  <div class="container">
    <div class="row text-center heading">
      <div class="col-md-2 col-md-offset-2 text-center">
        <div v-if="trading" @click="trading = false" class="well well-sm link">
          <span class="glyphicon glyphicon-menu-left"></span>
        </div>
      </div>
      <div class="col-md-4 title">
        <h1>NBA Trade Machine</h1>
      </div>
      <div v-if="!trading" class="col-md-4">
        <div v-if="teamCheck()" @click="startTrade()" class="col-md-6 well well-sm link">
          <span class="glyphicon glyphicon-menu-right"></span>
        </div>
      </div>
    </div>

    <div v-if="!trading" class="row text-center top">
      <div class="col-md-4 col-md-offset-2 team">
        <input v-model="team1.query" placeholder="Search for a team"><br><br>
      </div>

      <div class="col-md-4 team">
        <input v-model="team2.query" placeholder="Search for a team"><br><br>
      </div>
    </div>

    <div v-if="!trading" class="row">
      <div class="col-md-2 col-md-offset-2 text-left">
        <transition-group
          v-if="team1.query"
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
          <img v-if="team1.name!== 'Select Team 1'" src="http://image.ibb.co/ishqqa/basketball_3.png" alt="basketball_3">
        </div>
        <div class="col-md-6">
          <img v-if="team2.name!== 'Select Team 2'" src="http://image.ibb.co/jczybF/basketball_2.png" alt="basketball_2">
        </div>
      </div>

      <div v-if="team2.query" class="col-md-2 text-right">
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

    <Trade v-if="trading" :teamOne="team1.name" :teamTwo="team2.name"></Trade>

    <!-- <small class="credits text-right">
      <div>Icons made by <a href="http://www.flaticon.com/authors/popcorns-arts" title="Popcorns Arts">Popcorns Arts</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.freepik.com" title="Freepik">Freepik</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/darius-dan" title="Darius Dan">Darius Dan</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/nikita-golubev" title="Nikita Golubev">Nikita Golubev</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
      <div>Icons made by <a href="http://www.flaticon.com/authors/madebyoliver" title="Madebyoliver">Madebyoliver</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a></div>
    </small> -->
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
        team1: {
          'name': 'Select Team 1',
          'query': ''
        },
        team2: {
          'name': 'Select Team 2',
          'query': ''
        },
        teams1: teams,
        teams2: teams,
        trading: false
      }
    },
    computed: {
      team1Select: function () {
        var vm = this
        return this.teams1.filter(function (team) {
          return team.teamName.toLowerCase().indexOf(vm.team1.query.toLowerCase()) !== -1 && team.teamName !== vm.team2.name
        });
      },
      team2Select: function () {
        var vm = this
        return this.teams2.filter(function (team) {
          return team.teamName.toLowerCase().indexOf(vm.team2.query.toLowerCase()) !== -1 && team.teamName !== vm.team1.name
        });
      }
    },
    methods: {
      selectTeam: function(team, selection) {
        this[team].name = selection;
        this[team].query = selection;
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
        if (this.team1.name !== 'Select Team 1' && this.team2.name !== 'Select Team 2') {
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
  color: #C4CFD5;
  background-color: #000;
  font-family: 'Palanquin', sans-serif;
}

.heading {
  font-family: 'News Cycle', sans-serif;
  color: #f1f4ff;
}
.heading .well {
  margin-top: 20px;
  margin-bottom: 10px;
}
.title {

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
  transition: .3s ease-out;
}

ul {
  padding-left: 0px;
  overflow: scroll;
  max-height: 400px;
}

li.selection {
  color: #C4CFD5;

  list-style: none;
  border: 1px #C4CFD5;
}
li.selection:hover {
  text-decoration: underline;
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
  color: #ef586b;
}

.well {
  background-color: transparent;
  transition: .3s ease-out;
  font-family: 'Palanquin', sans-serif;
  color: #C4CFD5;
}
.well:hover {
  background-color: #ed8d1f;
  cursor: pointer;
  border: 1px solid #ed8d1f;
}

</style>
