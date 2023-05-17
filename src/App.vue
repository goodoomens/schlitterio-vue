<template>
  <div class="game-container" :style="`--tile-count: ${tileCount};`">
    <GameLogo />

    <div class="game-gui">
      <div class="game-ctrl">
        <button class="btn btn-start" @click="startGame">Start</button>
        <GameScore :score="score.pts" />
      </div>

      <HighscoreList
        :highscores="highscores"
        :lastPlayedScore="lastPlayedScore"
      />
    </div>

    <GameBoard :tileCount="tileCount" :snake="snake" :food="food" />

    <div class="game-btn-wrapper">
      <button class="btn btn-ctrl" @click="changeDirection('left')">
        <SvgIcon type="mdi" :size="42" :path="mdiChevronLeft" />
      </button>
      <button class="btn btn-ctrl" @click="changeDirection('right')">
        <SvgIcon type="mdi" :size="42" :path="mdiChevronRight" />
      </button>
    </div>
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
  --col-prim: #91dd2e;
  --col-sec: #37a35f;
  --col-food-apple: #e03131;
  --col-light-grey: #f3f3f3;
  --col-txt-prim: #ffffff;
  --col-txt-sec: #c0c0c0;
  --col-txt-dark: #444444;
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
  background-color: var(--col-light-grey);
  font-family: "Inter", sans-serif;
}

html,
body,
.game-container {
  height: 100dvh;
}

/* Layout */
.game-container {
  margin: auto;
  padding: 2rem;
  max-width: 70rem;

  display: flex;
  flex-direction: column;
  gap: 2rem;
}
.game-container > * {
  min-width: 0;
}
.game-gui {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.game-ctrl {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
.game-btn-wrapper {
  display: flex;
  gap: 1rem;
}

/* Buttons */
.btn {
  background-color: var(--col-prim);
  border: 0;
  border-bottom: 0.5rem solid var(--col-sec);
  border-radius: 1rem;
  color: white;
}
.btn:focus-visible {
  outline: 0.25rem dashed var(--col-prim);
  outline-offset: 0.25rem;
}
.btn:active {
  border-bottom: 0.25rem solid var(--col-sec);
}
.btn-start {
  padding: 1rem 2rem;

  font-size: 1.6rem;
  font-weight: 600;
  text-transform: uppercase;
}
.btn-ctrl {
  width: 100%;
  padding: 1rem;
}

/* Media queries */
@media (max-width: 380px) {
  html {
    font-size: 50%;
  }
  .game-container {
    gap: 1rem;
  }
  .game-ctrl {
    flex-direction: row;
  }
}
</style>
