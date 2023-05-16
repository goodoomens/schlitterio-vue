<template>
  <div class="game-logo">
    <picture>
      <source srcset="@/assets/snake_logo_sm.webp" type="image/webp" />
      <source srcset="@/assets/snake_logo_sm.png" type="image/png" />
      <img
        class="game-logo-img"
        src="@/assets/snake_logo_sm.png"
        alt="Schlitterio Logo"
      />
    </picture>
    <div class="game-logo-text">
      <h1 class="game-logo-title">Schlitterio</h1>
      <h2 class="game-logo-subtitle">Grab them tasty applez!</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: "GameLogo",
};
</script>

<style>
.game-logo {
  justify-self: flex-start;
  display: flex;
  align-items: center;
  gap: 1rem;
}
.game-logo-img {
  display: block;
  width: 6rem;
}
.game-logo-text {
  line-height: 1.2;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.game-logo-title {
  font-size: 2.8rem;
  font-weight: 800;
  color: var(--col-prim-shad0);
}
.game-logo-subtitle {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--col-prim-shad1);
}
</style>
