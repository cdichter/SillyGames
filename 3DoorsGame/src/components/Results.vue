<template>
  <div>
    <h2>Results</h2>
    <p>Wins: {{ results.wins }}</p>
    <p>Losses: {{ results.losses }}</p>
    <button @click="$emit('restart')">Play Again</button>
  </div>
</template>

<script>
export default {
  props: {
    results: { type: Object, required: true },
  },
};
</script>

<style scoped>
@media (max-width: 600px) {
  div {
    padding: 10px;
    text-align: center;
  }

  button {
    width: 100%;
  }
}
</style>
