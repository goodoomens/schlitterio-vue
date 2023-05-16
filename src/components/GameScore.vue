<template>
  <div class="game-score">
    <SvgIcon type="mdi" :size="28" :path="mdiFoodApple" />
    {{ score }}
  </div>
</template>

<script>
import SvgIcon from "@jamescoyle/vue-icon";
import { mdiFoodApple } from "@mdi/js";

export default {
  name: "GameScore",
  components: { SvgIcon },
  props: {
    score: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      mdiFoodApple,
    };
  },
};
</script>

<style>
.game-score {
  justify-self: flex-end;

  display: flex;
  align-items: center;
  gap: 0.6rem;

  font-size: 2rem;
  font-weight: 800;
  color: var(--col-food-apple);
}
</style>
