<template>
  <ol class="highscore-list" v-show="highscores.length > 0">
    <li
      class="highscore-item"
      v-for="(highscore, idx) in highscores"
      :key="highscore.id"
    >
      <span class="highscore-place">
        {{ idx + 1 }}
      </span>
      <span class="highscore-points"> {{ highscore.pts }} Applez </span>
      <span v-if="lastPlayedScore && lastPlayedScore.id === highscore.id">
        [Last]
      </span>
    </li>
  </ol>
</template>

<script>
export default {
  name: "HighscoreList",
  props: {
    highscores: {
      type: Array,
      required: true,
    },
    lastPlayedScore: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style>
.highscore-list {
  list-style: none;
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--col-txt-dark);

  display: flex;
  flex-direction: column;
}

.highscore-place {
  width: 2rem;
  height: 2rem;
  background-color: #ffffff;
  border-radius: 0.5rem;

  font-weight: 800;
  color: var(--col-txt-sec);

  display: flex;
  align-items: center;
  justify-content: center;
}

.highscore-item {
  padding: 0.2rem 0;
  display: flex;
  align-items: center;
  gap: 1.2rem;
}
</style>
