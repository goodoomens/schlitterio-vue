<template>
  <div class="game-board">
    <div
      class="tile"
      :class="getTileClass(index % tileCount, Math.floor(index / tileCount))"
      v-for="(tile, index) in Array(tileCount * tileCount)"
      :key="index"
      @click="
        () => console.log(index % tileCount, Math.floor(index / tileCount))
      "
    ></div>
  </div>
</template>

<script>
import methodsMixin from "@/mixins/methodsMixin";

export default {
  name: "GameBoard",
  mixins: [methodsMixin],
  props: {
    tileCount: {
      type: Number,
      required: true,
    },
    snake: {
      type: Object,
      required: true,
    },
    food: {
      type: Object,
      required: true,
    },
  },
  methods: {
    getTileClass(x, y) {
      const head = this.snake[0];
      return {
        "snake-head": head.x === x && head.y === y,
        "snake-body": this.isSnake(x, y) && !(head.x === x && head.y === y),
        food: this.isFood(x, y),
      };
    },
  },
};
</script>

<style>
.game-board {
  width: 100%;

  grid-column: 1 / -1;
  align-self: center;

  box-shadow: 0 0.5rem 2rem -1rem rgba(0, 0, 0, 0.1);
  border-radius: 1rem;

  display: grid;
  grid-template-columns: repeat(var(--tile-count), 1fr);
  align-content: start;

  gap: 0.2rem;
}
.tile {
  aspect-ratio: 1/1;
  background-color: #ffffff;
  border-radius: 0.25rem;
}
.snake-head {
  background-color: var(--col-sec);
}
.snake-body {
  background-color: var(--col-prim);
}
.food {
  background-color: var(--col-food-apple);
}
</style>
