<template>
  <md-card class="players-list" md-with-hover>
    <md-ripple>
      <md-card-header>
        <div class="md-title">Players</div>
      </md-card-header>

      <md-card-content class="players-list-content">
        <md-list>
          <md-list-item v-for="(player, index) in formattedPlayers" :key="index" :class="determineWrapperClass(player)">
            <md-avatar :class="player.color">{{player.initials}}</md-avatar>
            <span class="md-list-item-text">{{player.name}}</span>
            <span class="score">{{player.score}}</span>
          </md-list-item>
        </md-list>
      </md-card-content>

      <div class="btn-container" v-if="!hasStarted && formattedPlayers.length >= 3 && isVIP">
        <md-button class="md-dense md-raised md-primary" @click="beginGame">Start Game</md-button>
      </div>
      <div class="btn-container" v-else-if="!hasStarted && isVIP">
         <md-button class="md-raised" disabled>Start Game</md-button>
         <md-tooltip md-direction="bottom">Must have a minimum of 3 players</md-tooltip>
      </div>
      <div class="btn-container" v-else-if="round.isComplete && me.isCardzar" >
        <md-button class="md-dense md-raised md-primary" @click="nextRound">Next Round</md-button>
      </div>
    </md-ripple>
  </md-card>
</template>

<script>
import { mapState } from "vuex";
export default {
  name: "CurrentPlayers",
  updated() {
    console.log("updated");
    console.log(this.$store.state);
    console.log(this.players);
  },
  computed: {
    ...mapState(["players", "hasStarted", "round", "isVIP", "me"]),
    formattedPlayers() {
        let colorNum = 1;
      return this.players.map(player => {
        const { name, score, isCardzar } = player;
        const initials = name
          .split(" ")
          .slice(0, 2)
          .map(i => i[0])
          .join("")
          .toUpperCase();

        const color = `player-avatar--${colorNum}`
        colorNum = colorNum >= 5 ? 1 : colorNum + 1
        return {
          name,
          score,
          initials,
          isCardzar,
          color
        };
      });
    }
  },
  methods: {
    beginGame() {
      this.$store.dispatch("beginGame", this.$route.params.gameId);
    },
    nextRound() {
      this.$store.dispatch("nextRound", this.$route.params.gameId);
    },
    determineWrapperClass(player) {
      console.log('DWC', player, this.round)
      if(player.isCardzar) return 'cardzar';
      if(this.round.winner && player.name === this.round.winner.name) return 'winner';
      return '';
    }
  }
};
</script>

<style>
.players-list {
  position: absolute !important;
  left: 0;
  min-width: 250px;
  padding: 10px 0;
}
.score {
  font-weight: 600;
  color: #448aff;
}

.btn-container {
  margin: 0 auto;
  text-align: center;
}
.md-avatar {
  color: white;
  text-align: center;
  font-size: 14px;
}

.cardzar {
    background: #00bcd459;
    border-radius: 30px;
    margin: 5px 0;
}

.winner {
  background: #00d41c59;
  border-radius: 30px;
  margin: 5px 0;
}
.players-list-content {
  max-height: 350px;
  overflow: scroll;
}

.player-avatar--1 {
    background-color: #673ab7
}

.player-avatar--2 {
    background-color: #009688
}

.player-avatar--3 {
    background-color: #f44336
}

.player-avatar--4 {
    background-color: #3f51b5
}

.player-avatar--5 {
    background-color: #607d8b
}
</style>