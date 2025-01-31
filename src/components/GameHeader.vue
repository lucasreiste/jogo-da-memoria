<template>
  <header class="game-header">
    <div>Jogador: {{ playerName || "Anônimo" }}</div>
    <div>Pares encontrados: {{ matchedPairs }}/{{ totalPairs }}</div>
    <button @click="$emit('show-hint')" :disabled="isProcessing">
      Ver Dica
    </button>
    <button @click="$emit('change-user')">Trocar Usuário</button>

    <div class="header-ranking">
      <h3>Ranking</h3>
      <ul v-if="ranking.length">
        <li v-for="(item, index) in ranking" :key="index">
          {{ index + 1 }}º - {{ item.name }}: Levou {{ item.rounds }} rodadas
          para vencer
        </li>
      </ul>
      <p v-else>Sem informações de ranking</p>
    </div>
  </header>
</template>

<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  playerName: String,
  matchedPairs: Number,
  totalPairs: Number,
  isProcessing: Boolean,
  ranking: Array,
});

const emit = defineEmits(["show-hint", "change-user"]);
</script>

<style scoped>
.game-header {
  position: sticky;
  top: 0;
  background: rgba(44, 62, 80, 0.9);
  padding: 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  z-index: 1;
}

.game-header button {
  width: auto;
  min-width: 100px;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  background: #3498db;
  color: white;
  cursor: pointer;
}

.game-header button:disabled {
  background: #95a5a6;
  cursor: not-allowed;
}

.header-ranking {
  margin-top: 10px;
  text-align: center;
}
</style>
