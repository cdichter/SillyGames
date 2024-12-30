<template>
  <div class="game-container">
    <!-- Logo Section -->
    <header class="logo-container">
      <img
        src="/combined-logo.svg"
        alt="Checkers and Rally's Logo"
        class="logo"
      />
    </header>

    <main>
      <h1 class="spacer prompt"  v-if="!preferredPrizeChosen">Which prize are you going for?</h1>
      <div class="carousel" v-if="!preferredPrizeChosen">
        <div
          v-for="prize in prizes"
          :key="prize.name"
          class="carousel-item"
          @click="selectPreferredPrize(prize.name)"
          :class="{ selected: prize.name === preferredPrize }"
        >
          <img :src="prize.imageUrl" :alt="prize.name" />
          <h2>{{ prize.name }}</h2> <!-- Use h2 for prize name -->
        </div>
      </div>

      <div v-if="preferredPrizeChosen && !gameOver">
        <h2 class="spacer prompt">Select a door:</h2>
        <div class="doors">
          <Door
            v-for="(door, index) in doors"
            :key="index"
            :door="door"
            :index="index"
            :is-revealed="door.isRevealed"
            @select="handleDoorSelection"
          />
        </div>

        <div v-if="selectedDoor !== null">
          <h2>You selected "{{ selectedDoor + 1 }}".</h2>
          <button @click="revealNonPreferredDoor">Reveal a Door</button>
        </div>

        <div v-if="revealStage">
          <h2>Do you want to stay with your choice or switch to another door?</h2>
          <button @click="finalDecision('stay')">Stay</button>
          <button @click="finalDecision('switch')">Switch</button>
        </div>
      </div>

      <div v-if="gameOver">
        <!-- if they won the prize they wanted  preferredPrize = winnerPrize.name  -->
         <div v-if="preferredPrize === winnerPrize.name">
          <h2 class="spacer prompt">Congradulations: you won the prize that you wanted!</h2>
        </div>
        <div v-else>
            <h2 class="spacer prompt">You won this fine prize:</h2>
          </div>
        <div class="winner-prize">
          <img :src="winnerPrize.imageUrl" :alt="winnerPrize.name" />
          <h2>{{ winnerPrize.name }} has been added to your wallet</h2>
        </div>
        <h2 class="spacer prompt">Thanks for playing</h2>
      </div>
    </main>
  </div>
</template>


<script>
import Door from "./Door.vue";

export default {
  components: { Door },
  data() {
    return {
      prizes: [
        {
          name: "Big Buford",
          imageUrl:
            "/20230891_Big-Buford-Combo-1.jpg"        },
        {
          name: "Baconzilla",
          imageUrl:
            "/20230891_Baconzilla-Combo.jpg"
        },
        {
          name: "Fry-Seasoned Chicken Tenders",
          imageUrl:
            "/20230891_Fry-Seasoned-Chicken-Tenders-Combo.jpg"
        }
      ],
      doors: [],
      selectedDoor: null,
      preferredPrize: null,
      preferredPrizeChosen: false,
      revealStage: false,
      gameOver: false,
      winnerPrize: null
    };
  },
  methods: {
    initializeGame() {
      const shuffledPrizes = [...this.prizes].sort(() => 0.5 - Math.random());
      this.doors = shuffledPrizes.map(prize => ({
        prize,
        isSelected: false,
        isRevealed: false
      }));
    },
    handleDoorSelection(index) {
      this.selectedDoor = index;
    },
    selectPreferredPrize(prizeName) {
      this.preferredPrize = prizeName;
      this.preferredPrizeChosen = true;
    },
    revealNonPreferredDoor() {
      const nonPreferredDoors = this.doors
        .map((door, index) => ({ ...door, index }))
        .filter(
          door =>
            door.index !== this.selectedDoor &&
            door.prize.name !== this.preferredPrize &&
            !door.isRevealed
        );

      const doorToReveal =
        nonPreferredDoors[Math.floor(Math.random() * nonPreferredDoors.length)];
      this.doors[doorToReveal.index].isRevealed = true;
      this.revealStage = true;
    },
    finalDecision(choice) {
      let selectedPrize;

      if (choice === "stay") {
        // Keep the originally selected door
        selectedPrize = this.doors[this.selectedDoor].prize;
      } else if (choice === "switch") {
        // Switch to the other unopened door
        const otherDoor = this.doors.find(
          (door, index) => !door.isRevealed && index !== this.selectedDoor
        );
        selectedPrize = otherDoor.prize;
      }

      // Set the winnerPrize and end the game
      this.winnerPrize = selectedPrize;
      this.gameOver = true;
    }
  },
  created() {
    this.initializeGame();
  }
};
</script>
<style scoped>
/* General Styling */
#app {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  font-size: 1em;
}

.game-container {
  width: 100vw;
  height: 100vh;
  font-family: Arial, sans-serif;
  text-align: center;

}

/* Logo Section */
.logo-container {
  max-height: 15vh; /* 15% of the screen height */
  max-width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f4f4f4; /* Optional background */
}

.logo {
  max-width: 90vh; /* Prevent stretching */
  object-fit: contain;
}
h1 {
  font-size: 2em; /* Increase the font size of h2 elements */
  max-width: 80vw;
  word-wrap: break-word;
}

h2 {
  font-size: 1em; /* Increase the font size of h2 elements */
  max-width: 100vw;
  text-align: center;
  word-wrap: break-word;
}

/* Headings with Spacer */
.spacer {
  margin-top: 2rem; /* Add vertical separation */
}

/* Carousel Styling */
/* it must fit the screen width, and the height must be 30\% of the screen height. */
.carousel {
  display: flex;
  gap: 10px;
  width: 90vw;
  overflow-x: scroll;
  scroll-behavior: smooth;
  padding:  0px;
}

.carousel-item {
  flex: 0 0 auto;
  text-align: center;
  max-width: min-content;
  word-wrap: break-word;
  cursor: pointer;
  transition: transform 0.3s, box-shadow 0.3s;
}

.carousel-item img {
  width: 28vw;
  height: auto;
  border-radius: 50%;
  border: 3px solid transparent;
  transition: border 0.3s;
}
.carousel-item.selected {
  border: 3px solid #4caf50;
  transform: scale(1.1);
}

/* Doors Styling */
.doors {
  display: flex;
  gap: 20px;
  justify-content: center;
  align-items: center;
  flex-wrap: nowrap;
  overflow-x: auto;
  padding: 10px 0;
}

.door {
  width: 120px;
  height: 200px;
  text-align: center;
  border: 2px solid #8b4513;
  border-radius: 8px;
  background-color: #f4a261;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: transform 0.3s;
}

.winner-prize img {
  width: 30vh;
  height: auto;
}
</style>
