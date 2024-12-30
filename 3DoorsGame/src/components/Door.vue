<template>
  <div :class="['door', { selected: isSelected, revealed: isRevealed }]" @click="selectDoor">
    <div class="door-panel">
      <div v-if="!isRevealed" class="door-number">{{ index + 1 }}</div>
      <div v-if="isRevealed" class="prize-image">
        <img :src="door.prize.imageUrl" :alt="door.prize.name" />
      </div>
      <div v-if="isRevealed" class="door-name">{{ door.prize.name }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    door: { type: Object, required: true },
    index: { type: Number, required: true },
    isRevealed: { type: Boolean, default: false },
  },
  computed: {
    isSelected() {
      return this.index === this.$parent.selectedDoor;
    },
  },
  methods: {
    selectDoor() {
      this.$emit('select', this.index);
    },
  },
};
</script>

<style scoped>
.door {
  width: 120px;
  height: 200px;
  background-color: #8b4513; /* Brown color for a wooden look */
  border: 3px solid #663300; /* Darker brown for door frame */
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  position: relative;
  border-radius: 8px;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.3);
  transition: transform 0.3s;
}

.door:hover {
  transform: scale(1.05);
}

.door-panel {
  width: 90%;
  height: 90%;
  background-color: #cd853f; /* Lighter wood color for panel */
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.prize-image img {
  max-width: 100%;
  height: auto;
}

@media (max-width: 600px) {
  .door {
    width: 80px;
    height: 140px;
  }
}
</style>
