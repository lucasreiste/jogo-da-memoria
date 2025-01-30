<template>
  <div class="card" @click="flipCard">
    <div class="card-inner">
      <div v-if="shouldBeVisible" class="card-content">
        {{ props.emoji.emoji }}
      </div>
    </div>
  </div>
</template>
<script setup>
import { defineProps, computed } from "vue";

const props = defineProps(["emoji", "isMatched", "firstCard", "secondCard"]);

const emit = defineEmits(["flippedCard"]);

const flipCard = () => {
  emit("flippedCard", props.emoji);
};

const shouldBeVisible = computed(() => {
  if (
    props.isMatched ||
    props.firstCard?.cardId === props.emoji?.cardId ||
    props.secondCard?.cardId === props.emoji?.cardId
  ) {
    return true;
  }
  return false;
});
</script>

<style>
.card {
  width: 100px;
  height: 100px;
  background: rgb(69, 187, 142);
  border-radius: 10px;
}

.card-inner {
  width: 100%;
  height: 100%;
}
.card-content {
  display: flex;
  height: 100%;
  justify-content: center;
  align-items: center;
}
</style>
