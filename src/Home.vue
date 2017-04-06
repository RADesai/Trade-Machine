<template>
  <div class="container">
    <div class="row text-center heading">
      <div class="col-md-8 col-md-offset-2">
        <h1>NBA Trade Machine</h1>
        <hr class="orange">
      </div>
    </div>

    <div v-if="!trading" class="row">
      <br><h3>Select Participating Teams</h3><br>
    </div>

    <div v-if="!trading" class="row text-center top">
      <div class="col-md-6 team">
        <h4>{{ team1 }}</h4>
        <input v-model="query1" placeholder="Search for a team"><br><hr>
        <img v-if="team1!== 'Select Team 1'" src="http://image.ibb.co/ishqqa/basketball_3.png" alt="basketball_3" border="0">
      </div>

      <div class="col-md-6 team">
        <h4>{{ team2 }}</h4>
        <input v-model="query2" placeholder="Search for a team"><br><hr>
        <img v-if="team2!== 'Select Team 2'" src="http://image.ibb.co/jczybF/basketball_2.png" alt="basketball_2" border="0">
      </div>
    </div>

    <div v-if="!trading" class="row">
      <div class="col-md-6">
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

      <div v-if="query2" class="col-md-6">
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
          >{{ team.teamName }}</li>
        </transition-group>
      </div>
    </div>
    <hr v-if="!trading" class="orange">

    <Trade v-if="trading" :teamOne="team1" :teamTwo="team2"></Trade>
    <hr class="orange">

    <div v-if="teamCheck() && !trading" class="row text-center">
      <div class="col-md-4 col-md-offset-4">
        <!-- <router-link :to="{ path: '/trade', params: { ${team1}${team2} } }"> -->
        <div @click="startTrade()" class="well well-sm link">
          <span class="glyphicon glyphicon-menu-right"></span>
        </div>
        <!-- </router-link> -->
      </div>
    </div>

    <div class="row">
      <div v-if="trading" @click="trading = false" class="col-md-4 col-md-offset-1 text-center">
        <div class="well well-sm link">
          <span class="glyphicon glyphicon-menu-left"></span>
        </div>
      </div>
    </div>

    <!-- <small class="credits text-right">
      <div>Icons made by <a href="http://www.flaticon.com/authors/popcorns-arts" title="Popcorns Arts">Popcorns Arts</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a>
      <div>Icons made by <a href="http://www.freepik.com" title="Freepik">Freepik</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a>
      <div>Icons made by <a href="http://www.flaticon.com/authors/darius-dan" title="Darius Dan">Darius Dan</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a>
      <div>Icons made by <a href="http://www.flaticon.com/authors/nikita-golubev" title="Nikita Golubev">Nikita Golubev</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a>
      <div>Icons made by <a href="http://www.flaticon.com/authors/madebyoliver" title="Madebyoliver">Madebyoliver</a> from <a href="http://www.flaticon.com" title="Flaticon">www.flaticon.com</a>
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
          return team.teamName.toLowerCase().indexOf(vm.query1.toLowerCase()) !== -1
        });
      },
      team2Select: function () {
        var vm = this
        return this.teams2.filter(function (team) {
          return team.teamName.toLowerCase().indexOf(vm.query2.toLowerCase()) !== -1
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

.top {
  /*background-color: #CAD3D8;*/
}

input {
  width: 100%;
  background-color: transparent;
  border-color: transparent;
  border-radius: 6px 6px 8px 8px;
  color: #00a2a5;
  text-align: center;
  /*border-color: #ED8D1F;*/
  border-color: #00a2a5;
}
input:hover {
}
input:focus {
  outline: none;
}

.team {
  padding: 15px;
  border-top: 4px solid #00A2A5;
  transition: .3s ease-out;
}
.team:hover {
  /*box-shadow: 0px 2px 4px #00a2a5;*/
}

li.selection {
  /*border-bottom: 1px solid #00a2a5;*/
  list-style: none;
}
li.selection:hover {
  cursor: pointer;
  color: #00a2a5;
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
  color: #ed8d1f;
}
.well:hover {
  background-color: #ed8d1f;
  color: #000;
  cursor: pointer;
  border: 1px solid #ed8d1f;
}

</style>
