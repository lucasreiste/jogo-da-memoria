<template>
  <main class="game-container">
    <div v-if="isGameWon" class="win-message">
      Parabéns {{ playerName || "Jogador" }}! Você venceu!
      <p>Rodadas utilizadas: {{ rounds }}</p>
      <button class="restart-button" @click="restartGame">
        Reiniciar Jogo
      </button>
    </div>

    <div v-if="!isGameStarted" class="start-screen">
      <h1>Jogo da Memória</h1>

      <label for="playerName">Digite seu nome para iniciarmos:</label>
      <input
        type="text"
        v-model="playerName"
        placeholder="Digite seu nome"
        class="input-name"
      />
      <button class="start-button" @click="startGame">Iniciar Jogo</button>
    </div>

    <div v-else class="game-content">
      <GameHeader
        :playerName="playerName"
        :matchedPairs="matchedPairs"
        :totalPairs="totalPairs"
        :isProcessing="isProcessing"
        :ranking="ranking"
        @show-hint="showHint"
        @change-user="changeUser"
      />

      <div class="memory-game">
        <MemoryCard
          v-for="card in cards"
          :key="card.id"
          :content="card"
          :is-flipped="isCardFlipped(card)"
          :disabled="isProcessing"
          @flipped-card="handleCardFlip"
        />
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, computed, watch } from "vue";
import MemoryCard from "./components/MemoryCard.vue";
import GameHeader from "./components/GameHeader.vue";

const playerName = ref("");
const isGameStarted = ref(false);
const cards = ref([]);
const flippedCards = ref([]);
const matchedCards = ref([]);
const isProcessing = ref(false);
const isHintActive = ref(false);
const rounds = ref(0);
const ranking = ref([]);

const EMOJIS = ["😀", "😎", "🥳", "🤩", "😴", "😲", "🤯", "🤪", "😇", "😈"];
const FLIP_DELAY = 1000;
const HINT_DELAY = 2000;

const matchedPairs = computed(() => matchedCards.value.length / 2);
const totalPairs = computed(() => EMOJIS.length);
const isGameWon = computed(() => matchedPairs.value === totalPairs.value);

const isCardFlipped = (card) => {
  return (
    flippedCards.value.includes(card.id) ||
    matchedCards.value.includes(card.id) ||
    isHintActive.value
  );
};

const startGame = () => {
  isGameStarted.value = true;
  initializeGame();
};

const initializeGame = () => {
  const shuffledCards = [...EMOJIS, ...EMOJIS]
    .map((emoji) => ({ id: Math.random(), emoji }))
    .sort(() => Math.random() - 0.5);

  cards.value = shuffledCards;
  flippedCards.value = [];
  matchedCards.value = [];
  rounds.value = 0;
};

const handleCardFlip = (card) => {
  if (
    isProcessing.value ||
    flippedCards.value.includes(card.id) ||
    matchedCards.value.includes(card.id)
  )
    return;

  flippedCards.value.push(card.id);
  if (flippedCards.value.length === 2) {
    checkMatch();
  }
};

const checkMatch = () => {
  isProcessing.value = true;
  rounds.value++;
  const [firstId, secondId] = flippedCards.value;
  const firstCard = cards.value.find((card) => card.id === firstId);
  const secondCard = cards.value.find((card) => card.id === secondId);

  if (firstCard.emoji === secondCard.emoji) {
    matchedCards.value.push(firstId, secondId);
  }

  setTimeout(() => {
    flippedCards.value = [];
    isProcessing.value = false;
  }, FLIP_DELAY);
};

watch(isGameWon, (won) => {
  if (won) {
    ranking.value.push({
      name: playerName.value || "Anônimo",
      rounds: rounds.value,
    });
    ranking.value.sort((a, b) => a.rounds - b.rounds);
  }
});

const showHint = () => {
  isHintActive.value = true;
  setTimeout(() => {
    isHintActive.value = false;
  }, HINT_DELAY);
};

const restartGame = () => {
  playerName.value = "";
  rounds.value = 0;
  isGameStarted.value = false;
  initializeGame();
};

const changeUser = () => {
  playerName.value = "";
  isGameStarted.value = false;
};
</script>

<style>
body {
  margin: 0;
  min-height: 100vh;
  background: #69aedb;
  font-family: Arial, sans-serif;
  color: white;
}

.game-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.start-screen {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.input-name {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 16px;
}

.start-button {
  width: auto;
  min-width: 100px;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  background: #3498db;
  color: white;
  cursor: pointer;
}

.start-button:disabled {
  background: #95a5a6;
  cursor: not-allowed;
}

.game-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.memory-game {
  flex: 1;
  display: grid;
  gap: 30px;
  padding: 20px;
  margin: 0 auto;
  width: auto;
  grid-template-columns: repeat(5, 100px);
  justify-content: center;
  grid-auto-rows: 100px;
}

@media (max-width: 768px) {
  .memory-game {
    grid-template-columns: repeat(4, 1fr);
    gap: 15px;
  }
}

@media (max-width: 500px) {
  .memory-game {
    grid-template-columns: repeat(3, 100px);
    gap: 10px;
  }
}

@media (max-width: 380px) {
  .memory-game {
    grid-template-columns: repeat(2, 100px);
    gap: 10px;
  }
}

.processing-message {
  text-align: center;
  padding: 10px;
  background: rgba(44, 62, 80, 0.9);
}

.win-message {
  text-align: center;
  padding: 20px;
}

.restart-button {
  width: auto;
  min-width: 100px;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  background: #3498db;
  color: white;
  cursor: pointer;
}

.restart-button:disabled {
  background: #95a5a6;
  cursor: not-allowed;
}

.ranking-section {
  text-align: center;
  margin-top: 20px;
}
</style>
