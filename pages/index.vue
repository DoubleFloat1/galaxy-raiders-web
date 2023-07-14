<template>
  <div id="canvas">
    <div id="deep-space" />

    <div id="menu">
      <button @click="startGame">Start game</button>
      
      <button @click="gotoLeaderboard">Leaderboard</button>
      
      <button @click="quit">Quit</button>
    </div>

    <div id="leaderboard">
      <h2 class="text" style="text-decoration: underline">Leaderboard</h2>

      <h4 class="text">1st - {{s1}}</h4>

      <h4 class="text">2nd - {{s2}}</h4>

      <h4 class="text">3rd - {{s3}}</h4>

      <button @click="gotoMenu">Back to menu</button>
    </div>

    <div id="space-field">
      <SpaceObject id="spaceship" class="spaceship" :data="spaceField.ship" resolution="2" />

      <SpaceObject class="asteroid" :data="asteroid" resolution="2"
                  :key="asteroid.center"
                  v-for="asteroid in spaceField.asteroids" />

      <SpaceObject class="missile" :data="missile" resolution="2"
                  :key="missile.center"
                  v-for="missile in spaceField.missiles" />

      <SpaceObject class="explosion" :data="explosion" resolution="2"
                  :key="explosion.center"
                  v-for="explosion in spaceField.explosions" />
    </div>
  </div>
</template>

<script setup>
const {
  data: spaceField,
  refresh: updateSpaceField
} = await $get("/space-field");

onMounted(() => {
  window.addEventListener("keydown", async (event) => {
    const keyToCommand = {
      "ArrowUp": "MOVE_SHIP_UP",
      "ArrowDown": "MOVE_SHIP_DOWN",
      "ArrowRight": "MOVE_SHIP_RIGHT",
      "ArrowLeft": "MOVE_SHIP_LEFT",
      "Space": "LAUNCH_MISSILE",
      "Escape": "PAUSE_GAME",
    };

    const command = keyToCommand[event.code];

    // Ignore if invalid key was pressed
    if (command === undefined) return;

    console.log(`Triggering command: ${command}`);
    await $post("/ship/commands", { command })
  });

  window.setInterval(updateSpaceField, 20);

  document.getElementById("space-field").style.display = "none";
  document.getElementById("leaderboard").style.display = "none";
})

function startGame() {
  document.getElementById("space-field").style.display = "block";
  document.getElementById("menu").style.display = "none";
}

function gotoLeaderboard() {
  document.getElementById("leaderboard").style.display = "block";
  document.getElementById("menu").style.display = "none";
}

function gotoMenu() {
  document.getElementById("menu").style.display = "block";
  document.getElementById("leaderboard").style.display = "none";
}

function quit() {
  document.getElementById("menu").style.display = "none";
}
</script>

<script>
import json from '../json/Leaderboard.json';

var score1 = json.games[0].score;
var score2 = json.games[1].score;
var score3 = json.games[2].score;

export default{
  data(){
    return{
      s1: score1,
      s2: score2,
      s3: score3
    }
  }
}
</script>

<style>
#canvas {
  height: calc(100vh - 4rem);
  width: calc(100vw - 4rem);

  padding: 2rem;

  background-color: #36bbf5;
  overflow: hidden;

  position: relative;

  display: flex;
  justify-content: center;
  align-items: center;
}

@keyframes slide {
  0% {
    transform: translate(1px);
  }
  50% {
    transform: translate(-1px);
  }
  100% {
    transform: translate(1px);
  }
}

#deep-space {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  background-image: url("~/assets/space.png");
  background-origin: content-box;
  animation: slide 3s linear infinite;

  position: absolute;
  z-index: 0;
}

#space-field {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  position: relative;
}

#menu {
  scale: 5;
  position: relative;
  z-index: 1;
}

#leaderboard {
  scale: 5;
  position: relative;
  z-index: 1;
}

.text {
  color: white;
}

button {
  margin-left: 5px;
  margin-right: 5px;
  margin-top: 5px;
  margin-bottom: 5px;
}

.spaceship {
  background-image: url("~/assets/spaceship.png");
}

.asteroid {
  background-image: url("~/assets/asteroid.png");
}

.missile {
  background-image: url("~/assets/missile.png");
}

.explosion {
  background-image: url("~/assets/explosion.png");
}
</style>
