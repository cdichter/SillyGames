<!-- App.vue -->
<template>
    <GameBoard />
</template>

<script>
  import GameBoard from './components/GameBoard.vue';

  export default {
    name: 'App',
    components: {
      GameBoard,
    },
  };
</script>

<style>
  /* You can add global styles here if needed */
</style>
