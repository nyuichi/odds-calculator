<template>
  <div class="px-4 pb-4">
    <div
      class="md:container flex items-center flex-wrap justify-evenly mx-auto"
    >
      <!-- You -->
      <div class="block text-center mt-4">
        <span class="block text-2xl font-semibold">You:</span>
        <Card
          v-for="i in [0, 1]"
          :key="`card-${i}`"
          class="mx-1 my-2"
          :class="{ 'add-card-animation': animationIndices.includes(i) }"
          :card-id="usedCards[i]"
          :removable="usedCards[i] !== -1"
          :selected="inputPosition === i"
          :width="75"
          @click="setInputPosition(i)"
          @remove-card="removeCard(i)"
        />
      </div>

      <!-- Opponent -->
      <div class="block text-center mt-4">
        <span class="block text-2xl font-semibold pb-1">Opponent:</span>
        <Card
          v-for="i in [2, 3]"
          :key="`card-${i}`"
          class="mx-1 my-2"
          :class="{ 'add-card-animation': animationIndices.includes(i) }"
          :card-id="usedCards[i]"
          :removable="usedCards[i] !== -1"
          :selected="inputPosition === i"
          :width="75"
          @click="setInputPosition(i)"
          @remove-card="removeCard(i)"
        />
      </div>

      <!-- Community -->
      <div class="block text-center mt-4">
        <span class="block text-2xl font-semibold pb-1">Community Cards:</span>
        <Card
          v-for="i in [4, 5, 6, 7, 8]"
          :key="`card-${i}`"
          class="mx-1 my-2"
          :class="{
            'add-card-animation': animationIndices.includes(i),
            'mx-6': i === 7,
          }"
          :card-id="usedCards[i]"
          :removable="usedCards[i] !== -1"
          :selected="inputPosition === i"
          :width="75"
          @click="setInputPosition(i)"
          @remove-card="removeCard(i)"
        />
      </div>
    </div>

    <!-- Win rate bar -->
    <div
      class="md:container flex items-center justify-center mx-auto mt-6 mb-10"
    >
      <div class="w-1/6 font-semibold">
        <span class="block text-center text-xl">You:</span>
        <span class="block text-center text-2xl">55.67%</span>
      </div>
      <div class="flex w-1/2 h-4 rounded-2xl justify-between bg-gray-300">
        <div class="bg-green-600 rounded-l-2xl" style="width: 55.67%" />
        <div class="bg-red-600 rounded-r-2xl" style="width: 43.26%" />
      </div>
      <div class="w-1/6 font-semibold">
        <span class="block text-center text-xl">Opponent:</span>
        <span class="block text-center text-2xl">43.26%</span>
      </div>
    </div>

    <InputBox :is-used="isUsed" @add-card="addCard($event)" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Card from "./Card.vue";
import InputBox from "./InputBox.vue";
export default defineComponent({
  name: "Main",
  components: { Card, InputBox },

  data: function () {
    let animationIndices: number[] = [];
    return {
      inputPosition: 0,
      animationIndices,
      isUsed: Array.from({ length: 52 }, () => false),
      usedCards: Array.from({ length: 9 }, () => -1),
    };
  },

  methods: {
    addCard: function (card: number) {
      if (!this.isUsed[card]) {
        this.isUsed[card] = true;
        this.usedCards[this.inputPosition] = card;
        if (!this.animationIndices.includes(this.inputPosition)) {
          this.animationIndices.push(this.inputPosition);
          setTimeout(() => this.animationIndices.shift(), 350);
        }
        this.inputPosition = Math.min(this.inputPosition + 1, 8);
      }
    },

    removeCard: function (pos: number) {
      this.isUsed[this.usedCards[pos]] = false;
      this.usedCards[pos] = -1;
    },

    setInputPosition: function (pos: number) {
      let com_first_empty = this.usedCards.slice(4, 9).indexOf(-1);
      if (pos === 1 && this.usedCards[0] === -1 && this.usedCards[1] === -1) {
        pos = 0;
      }
      if (pos === 3 && this.usedCards[2] === -1 && this.usedCards[3] === -1) {
        pos = 2;
      }
      if (4 <= pos && com_first_empty !== -1 && this.usedCards[pos] === -1) {
        pos = Math.min(pos, com_first_empty + 4);
      }
      this.inputPosition = pos;
    },
  },
});
</script>

<style scoped>
.add-card-animation {
  animation: bounce-in 0.3s;
}

@keyframes bounce-in {
  0% {
    transform: scale(1);
  }
  40% {
    transform: scale(1.15);
  }
  90% {
    transform: scale(0.95);
  }
  100% {
    transform: scale(1);
  }
}
</style>
