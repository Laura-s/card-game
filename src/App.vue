<template>
  <h1>Poro card game</h1>

  <section class="game-board">
    <Card
      v-for="(card, index) in cardList"
      :key="`card-${index}`"
      :matched="card.matched"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
    />
  </section>
  <h2>{{ status }}</h2>
  <h2>{{ timer }} </h2>
  <button @click="inProgres ? restartGame() : startGame()" class="restart-btn">
    {{ inProgres ? "Restart" : "Start" }}
  </button>
</template>

<script>
import _ from "lodash";
import { computed, ref, watch } from "vue";
import Card from "./components/Card";
import { launchConfetti } from "../src/utilities/confetti";
export default {
  name: "App",
  components: {
    Card,
  },

  setup() {
    const timer = ref(0)
    let inProgres = ref(false);
    let firstCard = ref("");
    let secondCard = ref("");
    const cardList = ref([]);
    
    const count = () =>{
      setInterval(() => {
        timer.value ++
      }, 1000);
    }
    // const userSelection = ref([]);
    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return "You won!";
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`;
      }
    });
    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        (card) => card.matched === false
      ).length;
      return remainingCards / 2;
    });
    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value);
    };

    const startGame = () => {
      count()
      inProgres.value = true;
       shuffleCards()
      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false,
        };
      });
    };
    const restartGame = () => {
      timer.value = 0
      firstCard.value = "";
      secondCard.value = "";
      setTimeout(shuffleCards, 500);
      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false,
        };
      });
    };
    const setupGame = () => {
       shuffleCards()
      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false,
        };
      });
      
    }
    const cardItems = [
      "poro1",
      "poro2",
      "poro3",
      "poro4",
      "poro5",
      "poro6",
      "poro7",
      "poro8",
    ];
    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      });
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      });
    });
    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      };
    });

    const checkMatched = () => {
      console.log(
        "check for matched",
        cardList.value[firstCard.value].value,
        cardList.value[secondCard.value].value
      );
      if (
        cardList.value[firstCard.value].value ===
        cardList.value[secondCard.value].value
      ) {
        cardList.value[firstCard.value].matched = true;
        cardList.value[secondCard.value].matched = true;
        cardList.value[firstCard.value].visible = true;
        cardList.value[secondCard.value].visible = true;
        firstCard.value = "";
        secondCard.value = "";
      }
    };
    const flipCard = (payload) => {

      if(!inProgres.value) return

      cardList.value[payload.position].visible = true;

      if (
        firstCard.value === payload.position ||
        secondCard.value === payload.position
      ) {
        console.log("aceasi");
        cardList.value[payload.position].visible = false;

        if (firstCard.value === payload.position) {
          console.log("aceasi unu");

          if (secondCard.value !== "") {
            firstCard.value = secondCard.value;

            secondCard.value = "";
            return;
          }
          firstCard.value = "";
          return null;
        }

        if (secondCard.value === payload.position) {
          console.log("aceasi doi");
          secondCard.value = "";
        }

        // firstCard.value = "";
        // secondCard.value = '';
        return null;
      }

      if (firstCard.value !== "" && secondCard.value !== "") {
        console.log("trei");

        cardList.value[firstCard.value].visible = false;
        firstCard.value = secondCard.value;
        secondCard.value = payload.position;
        checkMatched();
        return;
      }

      if (firstCard.value !== "" && secondCard.value === "") {
        console.log("doi");
        secondCard.value = payload.position;
        checkMatched();
        return;
        // cardList.value[firstCard.value].visible = false;
      }

      if (firstCard.value === "") {
        console.log("unu");
        firstCard.value = payload.position;
      }
    };

    watch(remainingPairs, (currentValue) => {
      if (currentValue === 0) {
        launchConfetti();
      }
    });

    return {
      cardList,
      flipCard,
      status,
      shuffleCards,
      restartGame,
      inProgres,
      startGame,
      setupGame,
      count,
      timer,
    };
  },
  mounted() {

    this.setupGame()
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-gap: 30px;
  justify-content: center;
}

.restart-btn {
  background-color: rgb(16, 38, 51);
  color: goldenrod;
  border: goldenrod 3px solid;
  padding: 5px 15px;
}
</style>
