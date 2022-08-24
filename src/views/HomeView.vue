<template>
  <div class="home">
    <header>
      <h1>Monster Slayer Game</h1>
    </header>
    <div id="game">
      <section id="monster" class="container">
        <h2>Monster</h2>
        <div class="healthbar">
          <div class="healthbar__value" :style="monsterHealthBar"></div>
        </div>
      </section>
      <section id="player" class="container">
        <h2>You</h2>
        <div class="healthbar">
          <div class="healthbar__value" :style="playerHealthBar"></div>
        </div>
      </section>
      <section class="container" v-if="winner">
        <h2>Game Over!</h2>
        <h3 v-if="winner === 'monster'">Monster Won!</h3>
        <h3 v-else-if="winner === 'player'">You Won!</h3>
        <h3 v-else>It's a draw!</h3>
        <button class="startgame" @click="startGame">Start New Game</button>
      </section>
      <section id="controls" v-else>
        <button class="attack" @click="attackmonster">ATTACK</button>
        <button
          class="specialattack"
          @click="specialattackmonster"
          :disabled="numOfSpecialAttack === 0"
        >
          SPECIAL ATTACK {{ numOfSpecialAttack }}
        </button>
        <button class="heal" @click="healplayer" :disabled="numOfHeal === 0">
          HEAL {{ numOfHeal }}
        </button>
        <button class="surrender" @click="surrender">SURRENDER</button>
      </section>
      <section id="log" class="container">
        <h2>Battle Log</h2>
        <ul>
          <li v-for="logMessage in messagesLog" v-bind:key="logMessage.index">
            <span
              :class="{
                'log--player': logMessage.actionBy === 'player',
                'log--monster': logMessage.actionBy === 'monster',
              }"
              >{{
                logMessage.actionBy === "player" ? "Player" : "Monster"
              }}</span
            >
            <span v-if="logMessage.actionType === 'heal'">
              heals himself for
              <span class="log--heal">{{ logMessage.actionValue }}</span></span
            >
            <span v-else>
              attacks and deals
              <span class="log--damage">{{ logMessage.actionValue }}</span>
            </span>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<script>
function getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}
export default {
  name: "HomeView",
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      numOfSpecialAttack: 3,
      numOfHeal: 3,
      // round: 0,
      winner: null,
      messagesLog: [],
    };
  },
  components: {},
  methods: {
    startGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.messagesLog = [];
      // this.round = 0;
      this.numOfHeal = 3;
      this.numOfSpecialAttack = 3;
      this.winner = null;
    },
    attackmonster() {
      // this.round++;
      const attackValue = getRandomValue(5, 12);
      if (this.monsterHealth - attackValue < 0) {
        this.monsterHealth = 0;
      } else {
        this.monsterHealth -= attackValue;
      }
      this.addMessagesLog("player", "attack", attackValue);
      this.attackplayer();
    },
    attackplayer() {
      const attackValue = getRandomValue(8, 15);
      // this.playerHealth -= attackValue;
      if (this.playerHealth - attackValue < 0) {
        this.playerHealth = 0;
      } else {
        this.playerHealth -= attackValue;
      }
      this.addMessagesLog("monster", "attack", attackValue);
    },
    specialattackmonster() {
      this.numOfSpecialAttack--;
      // this.round++;
      const attackValue = getRandomValue(10, 25);
      if (this.monsterHealth - attackValue < 0) {
        this.monsterHealth = 0;
      } else {
        this.monsterHealth -= attackValue;
      }
      this.addMessagesLog("player", "attack", attackValue);
      this.attackplayer();
    },
    healplayer() {
      // this.round++;
      this.numOfHeal--;
      const healValue = getRandomValue(8, 20);

      if (this.playerHealth + healValue > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += healValue;
      }
      this.addMessagesLog("player", "attack", healValue);
      this.attackplayer();
    },

    surrender() {
      // alert((this.winner = "Monster Winns !!"));
      this.winner = "monster";
    },
    addMessagesLog(who, what, value) {
      this.messagesLog.unshift({
        actionBy: who,
        actionType: what,
        actionValue: value,
      });
    },
  },
  computed: {
    monsterHealthBar() {
      if (this.monsterHealth < 0) {
        return { width: "0%" };
      } else {
        if (this.monsterHealth <= 50) {
          return { width: this.monsterHealth + "%", background: "red" };
        }
      }
      return { width: this.monsterHealth + "%" };
    },
    playerHealthBar() {
      if (this.playerHealth < 0) {
        return { width: "0%" };
      } else {
        if (this.playerHealth <= 50) {
          return {
            width: this.playerHealth + "%",
            background: "red",
          };
        }
      }
      return { width: this.playerHealth + "%" };
    },
    // specialAttackbar() {
    //   return this.round % 3 !== 0;
    // },
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "monster";
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "player";
      }
    },
  },
};
</script>
<style scoped>
* {
  box-sizing: border-box;
}
.attack {
  background-color: rgb(234, 48, 48);
}
.attack:hover {
  background-color: rgb(238, 138, 138);
}
.specialattack {
  background-color: #ffaf4f;
}
.specialattack:hover {
  background-color: #f5b465;
}
.heal {
  background-color: #76ff7e;
}
.heal:hover {
  background-color: rgb(30, 199, 75);
  /* background-color: rgb(148, 217, 44); */
}
.surrender {
  background-color: #fcfafa;
}
.surrender:hover {
  background-color: rgb(255, 255, 255);
}
.startgame {
  background-color: #76ff7e;
}

.startgame:hover {
  background-color: #aaffb0;
}
html {
  font-family: "Jost", sans-serif;
}

body {
  margin: 0;
}

header {
  padding: 0.5rem;

  color: black;
  text-align: center;
  margin-bottom: 2rem;
}

section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: #fde5e5;
}

.healthbar__value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}
h1 {
  color: rgba(0, 0, 0, 0.8);
}
#monster h2,
#player h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #d0d0d0;

  color: black;
  font-weight: 100;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 12rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  border-color: #e6dfe3;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #ff0000;
}

.log--monster {
  color: blue;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>
