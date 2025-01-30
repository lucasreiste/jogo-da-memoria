<template>
  <div class="container">
    <input type="text" v-model="nameInput" />
    <br />

    Jogador Atual: {{ nameInput }}
    <div class="memory-game">
      <MemoryCard
        v-for="emoji in optionsDoubled"
        :emoji="emoji"
        :first-card="firstCard"
        :second-card="secondCard"
        :is-matched="isMatched(emoji.id)"
        @flipped-card="flipCard"
      />
    </div>

    {{ firstCard }}
    {{ secondCard }}
    {{ matchedValues }}

    <span v-show="userWon">Parabéns! Você ganhou</span>
  </div>
</template>
<script setup>
import { computed, ref } from "vue";
import MemoryCard from "./components/MemoryCard.vue";

const nameInput = ref(null);
const options = ["Y", "😁", "😂", "😃", "😄"];

const matchedValues = ref([]);
const optionsDoubled = ref([]);

const firstCard = ref(null);
const secondCard = ref(null);

const shuffleCards = () => {
  const randomCards = options.map((emoji) => ({
    emoji,
    id: Math.random(),
  }));

  let doubledCards = [...randomCards, ...randomCards];

  doubledCards = doubledCards.map((emoji) => ({
    ...emoji,
    cardId: Math.random() + 1,
  }));

  doubledCards.sort(() => Math.random() - 0.5);
  optionsDoubled.value = doubledCards;
};

const flipCard = (emoji) => {
  console.log(emoji);
  if (!firstCard.value) {
    firstCard.value = emoji;
    return;
  }

  secondCard.value = emoji;

  checkResults();
};

const isMatched = (id) => {
  return matchedValues.value.some((card) => card.id === id);
};

const checkResults = () => {
  if (firstCard.value.id === secondCard.value.id) {
    // Adicionando apenas os IDs únicos ao array de matches
    if (!matchedValues.value.some((card) => card.id === firstCard.value.id)) {
      matchedValues.value.push(firstCard.value);
    }
    firstCard.value = null;
    secondCard.value = null;
    return;
  }
  setTimeout(() => {
    firstCard.value = null;
    secondCard.value = null;
  }, 1000);
};

const userWon = computed(() => {
  return matchedValues.value === options.value;
});

shuffleCards();
</script>
<style>
.memory-game {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  margin: 100px;
  gap: 10px;
}
</style>
