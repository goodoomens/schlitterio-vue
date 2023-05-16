<template>
  <div class="game-container" :style="`--tile-count: ${tileCount};`">
    <header class="game-header">
      <GameLogo />
      <GameScore :score="score.pts" />
    </header>

    <div class="game-gui">
      <button class="btn btn-start" @click="startGame">Start</button>
      <HighscoreList
        :highscores="highscores"
        :lastPlayedScore="lastPlayedScore"
      />
    </div>

    <GameBoard :tileCount="tileCount" :snake="snake" :food="food" />

    <button class="btn btn-ctrl" @click="changeDirection('left')">
      <SvgIcon type="mdi" :size="42" :path="mdiChevronLeft" />
    </button>
    <button class="btn btn-ctrl" @click="changeDirection('right')">
      <SvgIcon type="mdi" :size="42" :path="mdiChevronRight" />
    </button>
  </div>
</template>

<script>
import SvgIcon from "@jamescoyle/vue-icon";
import GameLogo from "@/components/GameLogo";
import GameScore from "@/components/GameScore";
import HighscoreList from "@/components/HighscoreList";
import GameBoard from "@/components/GameBoard";
import { mdiChevronLeft, mdiChevronRight } from "@mdi/js";

import helpersMixin from "@/mixins/helpersMixin";
import methodsMixin from "@/mixins/methodsMixin";

export default {
  title: "Schlitterio - Snake Game",
  mixins: [helpersMixin, methodsMixin],
  components: {
    SvgIcon,
    GameLogo,
    GameScore,
    HighscoreList,
    GameBoard,
  },
  data() {
    return {
      directions: ["up", "down", "left", "right"],
      tileCount: 20,

      snake: [
        {
          x: Math.floor(this.tileCount / 2),
          y: Math.floor(this.tileCount / 2),
        },
      ],
      food: {
        x: this.randomOf(this.tileCount),
        y: this.randomOf(this.tileCount),
      },

      isRunning: false,
      direction: "right",
      score: { id: null, pts: 0 },
      highscores: [],
      lastPlayedScore: null,

      // Icons
      mdiChevronLeft,
      mdiChevronRight,
    };
  },
  mounted() {
    this.loadHighscores();
    window.addEventListener("keydown", this.handleKeydown);
  },
  methods: {
    startGame() {
      this.resetGame();
      if (!this.isRunning) {
        this.isRunning = true;
        this.gameLoop();
      }
    },

    resetGame() {
      this.score.pts = 0;
      this.snake = [
        {
          x: Math.floor(this.tileCount / 2),
          y: Math.floor(this.tileCount / 2),
        },
      ];
      this.direction = this.directions[this.randomOf(this.directions.length)];
      this.food = {
        x: this.randomOf(this.tileCount),
        y: this.randomOf(this.tileCount),
      };
    },

    changeDirection(newDirection) {
      if (!this.isRunning) return;

      switch (this.direction) {
        case "right":
          this.direction = newDirection === "left" ? "up" : "down";
          break;
        case "left":
          this.direction = newDirection === "left" ? "down" : "up";
          break;
        case "up":
          this.direction = newDirection === "left" ? "left" : "right";
          break;
        case "down":
          this.direction = newDirection === "left" ? "right" : "left";
          break;
      }
    },

    gameLoop() {
      if (!this.isRunning) return;

      const newHead = this.getNewHead();

      if (this.isSnake(newHead.x, newHead.y)) {
        this.saveScore();
        this.isRunning = false;
        return;
      }

      this.snake.unshift(newHead);

      if (this.isFood(newHead.x, newHead.y)) {
        this.food = {
          x: this.randomOf(this.tileCount),
          y: this.randomOf(this.tileCount),
        };
        this.score.id = Date.now();
        this.score.pts += 1;
      } else {
        this.snake.pop();
      }

      setTimeout(this.gameLoop, 120);
    },

    getNewHead() {
      const head = this.snake[0];
      let newHead;

      switch (this.direction) {
        case "right":
          newHead = { x: (head.x + 1) % this.tileCount, y: head.y };
          break;
        case "left":
          newHead = {
            x: (head.x - 1 + this.tileCount) % this.tileCount,
            y: head.y,
          };
          break;
        case "up":
          newHead = {
            x: head.x,
            y: (head.y - 1 + this.tileCount) % this.tileCount,
          };
          break;
        case "down":
          newHead = { x: head.x, y: (head.y + 1) % this.tileCount };
          break;
      }

      return newHead;
    },

    saveScore() {
      const scores = JSON.parse(localStorage.getItem("highscores")) || [];
      const newScore = {
        id: this.score.id,
        pts: this.score.pts,
      };

      const scoreExists = scores.some((s) => s.id === newScore.id);
      if (scoreExists) return;

      scores.push(newScore);
      scores.sort((a, b) => b.pts - a.pts);
      scores.splice(3);

      while (scores.length < 3) {
        scores.push({ id: null, pts: 0 });
      }

      localStorage.setItem("highscores", JSON.stringify(scores));
      localStorage.setItem("lastPlayedScore", JSON.stringify(newScore));

      this.lastPlayedScore = newScore;
      this.highscores = scores;
    },

    loadHighscores() {
      try {
        const scores = JSON.parse(localStorage.getItem("highscores")) || [];
        const lastScore = JSON.parse(localStorage.getItem("lastPlayedScore"));

        while (scores.length < 3) {
          scores.push({ id: null, pts: 0 });
        }

        this.highscores = scores;

        if (lastScore) {
          this.lastPlayedScore = lastScore;
        }
      } catch (error) {
        console.error("Error loading top scores from local storage:", error);
      }
    },

    handleKeydown(e) {
      if (e.key === "ArrowLeft") {
        this.changeDirection("left");
      } else if (e.key === "ArrowRight") {
        this.changeDirection("right");
      }
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap");

/* Variables */
:root {
  --col-prim: #74b816;
  --col-prim-shad0: #3a5c0b;
  --col-prim-shad1: #5c940d;
  --col-prim-shad2: #e3f1d0;
  --col-prim-shad3: #f1f8e8;
  --col-food-apple: #e03131;
  --col-light-grey: #f3f3f3;
  --col-mid-grey: #adb5bd;

  --col-txt-prim: #ffffff;
  --col-txt-sec: #495057;
}

/* Global styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  touch-action: manipulation;
  user-select: none;
}
html {
  font-size: 62.5%;
  overflow: hidden;
}
body {
  background-color: var(--col-prim-shad2);
  font-family: "Inter", sans-serif;
}

html,
body,
.game-container {
  height: 100svh;
}

/* Layout */
.game-container {
  margin: auto;
  padding: 2rem;
  max-width: 56rem;

  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr 100fr 1fr;
  justify-content: center;
  gap: 1rem;
}
.game-container > * {
  min-width: 0;
}
.game-header {
  grid-column: 1 / -1;

  display: flex;
  align-items: center;
  justify-content: space-between;
}
.game-gui {
  grid-column: 1 / -1;

  display: flex;
  justify-content: space-between;
}

/* Buttons */
.btn {
  background-color: var(--col-prim);
  border: 0;
  border-bottom: 0.5rem solid var(--col-prim-shad1);
  border-radius: 1rem;
  color: white;
}
.btn:focus-visible {
  outline: 0.25rem dashed var(--col-prim);
  outline-offset: 0.25rem;
}
.btn:active {
  border-bottom: 0.25rem solid var(--col-prim-shad1);
}
.btn-start {
  justify-self: flex-start;
  align-self: center;
  padding: 1.2rem 2.4rem;

  font-size: 1.6rem;
  font-weight: 600;
  text-transform: uppercase;
}
.btn-ctrl {
  padding: 1rem;
  align-self: flex-end;
}
@media (max-width: 380px) {
  html {
    font-size: 50%;
  }
}
</style>
