<script>
import { reactive } from '@vue/reactivity'
import { invoke } from '@tauri-apps/api/tauri'

import supported_games_json from '../assets/supported-games.json'

export default {
  data() {
    return {
      supported_games: supported_games_json,
    };
  },
  setup() {
    const games = reactive({})
    return {games}
  },
  methods: {
    async scanGames() {
      await invoke('scan_games').then((entrys) => {
        entrys.forEach(element => {
          let game = JSON.parse(element)
          if(this.supported_games[game.appid]){
            this.games[game.appid] = game
          }
        })
      })
      this.$emit('on-scan-games')
    },

    sendDeployMods() {
      this.$emit('deploying-mods')
    },

    selectNewGame(e, gameEntry){
      const gameButton = e.target 
      const buttonList = this.$refs.game_ref
      buttonList.forEach(elem => {
        elem.className = ""
      })
      gameButton.className = "active"
      this.$emit('on-game-selected', gameEntry)
    }
  }
}
</script>

<template>
<div class="side-bar">
  <button class="scan-games-button" @click="scanGames()">Scan games</button>
  <div class="game-list">
    <li v-for="(game) in games" :key="game">
      <button ref="game_ref" @click="selectNewGame($event, game)">{{ game.name }}</button>
    </li>
  </div>
  <div class="options-bottom">
    <button class="run-button">Run</button>
    <button class="deploy-button" @click="sendDeployMods()">Deploy</button>
  </div>
  <div class="separator-right"></div>
</div>
</template>
  
<style scoped>
.side-bar {
  float: left;
  width: 215px;
  max-height: 720px;
  overflow: hidden;
}
.scan-games-button {
  width: 175px;
  height: 50px;
  margin-left: 20px;
  margin-top: 15px;
  margin-bottom: 10px;
  margin-right: 20px;
  font-size: 20px;
}
.game-list {
  background-color: rgba(255,255,255,7%);
  border-radius: 5px;
  padding: 1px;
  margin-left: 20px;
  margin-right: 20px;
  min-height: 596px;
  max-height: 596px;
  overflow: hidden;
  overflow-y: scroll
}
.game-list li {
  list-style: none;
}
.game-list button {
  text-align: left;
  display: block;
  width: 165px;
  margin: auto;
  margin-top: 5px;
  margin-bottom: 5px;
  background-color: rgba(255,255,255,0%);
  color: rgba(255,255,255,100%);
}
.game-list button:hover {
  background-color: rgba(255,255,255,4%);
  color: rgba(255,255,255,100%);
}
.game-list .active, .game-list .active:hover {
  background-color: rgba(255,255,255,9%);
  color: rgba(255,255,255,100%);
}
.options-bottom {
  margin-top: 7px;
  margin-left: 20px;
}
.options-bottom button {
  height: 35px;
  margin-bottom: 4px;
  font-size: 16px
}
.options-bottom i {
  font-size: 16px;
}
.deploy-button {
  width: 115px;
  margin-left: 14px;
  margin-right: 20px;
}
.separator-right {
    color: black;
    background-color: rgba(255,255,255,8%);
    width: 1px;
    height: 720px;
    float: right;
    margin-left: 215px;
    position: absolute;
    top: 0;
    left: 0;
}
</style>