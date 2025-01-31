<template>
  <div
    class="card"
    :class="{ 'card-disabled': disabled, 'card-flipped': isFlipped }"
    @click="handleClick"
  >
    <div class="card-inner">
      <div class="card-front"></div>
      <div class="card-back" v-if="isFlipped">
        {{ content.emoji }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  content: Object,
  isFlipped: Boolean,
  disabled: Boolean,
});

const emit = defineEmits(["flipped-card"]);

const handleClick = () => {
  if (!props.disabled && !props.isFlipped) {
    emit("flipped-card", props.content);
  }
};
</script>

<style>
.card {
  width: 100px;
  height: 100px;
  cursor: pointer;
}

.card-inner {
  width: 100%;
  height: 100%;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.5s;
}

.card-flipped .card-inner {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 10px;
  backface-visibility: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 32px;
  font-weight: bold;
}

.card-front {
  background: linear-gradient(to bottom, #3498db, #2980b9);
}

.card-back {
  background: linear-gradient(to bottom, #f39c12, #e67e22);
  transform: rotateY(180deg);
}

.card-disabled {
  pointer-events: none;
  opacity: 0.6;
}
</style>
