<template>
  <div class="container mx-auto">
    <vue-slider
      v-model="range"
      :vData="ranges"
      :marks="true"
      :absorb="true"
      :width="`300px`"
      class="mx-auto my-6"
    ></vue-slider>
    <div class="text-2xl font-semibold mx-auto text-center">
      {{ (ratio * 100).toFixed(1) }}%
    </div>
    <table class="table-auto mt-6 mb-10 mx-auto">
      <tr v-for="i in Array(13).keys()" :key="`tr-${i}`">
        <td v-for="j in Array(13).keys()" :key="`td-${i}${j}`">
          <Hand
            :key="`hand-${i}${j}`"
            class="px-1 py-1"
            :hand-name="handNames[i][j]"
            :in-range="inRange[range][i][j]"
          />
        </td>
      </tr>
    </table>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import VueSlider from "vue-slider-component";
import "vue-slider-component/theme/default.css";
import Hand from "./Hand.vue";
export default defineComponent({
  name: "Range",
  components: { Hand, VueSlider },
  data: function () {
    const RANK_NAMES = `AKQJT98765432`.split("");
    const handNames: string[][] = Array(13);
    for (const i of Array(13).keys()) {
      handNames[i] = Array(13);
      for (const j of Array(13).keys()) {
        let handName =
          i < j ? RANK_NAMES[i] + RANK_NAMES[j] : RANK_NAMES[j] + RANK_NAMES[i];
        if (i < j) {
          handName += "s";
        } else if (j < i) {
          handName += "o";
        }
        handNames[i][j] = handName;
      }
    }

    const HAND_CATEGORIES: any = [
      ["C1", ["AA", "KK"]],
      ["C2", ["QQ", "AKs", "AKo", "JJ"]],
      ["C3", ["AQs", "AQo", "TT", "99"]],
      ["C4", ["AJs", "KQs", "88", "77"]],
      ["C5", ["AJo", "ATs", "ATo", "KQo", "KJs", "66", "55"]],
      [
        "C6",
        [
          "A9s",
          "A8s",
          "A7s",
          "A6s",
          "A5s",
          "A4s",
          "A3s",
          "A2s",
          "KJo",
          "KTs",
          "QJs",
          "QTs",
          "JTs",
          "44",
          "33",
          "22",
        ],
      ],
      [
        "C7",
        [
          "A9o",
          "A8o",
          "A7o",
          "A6o",
          "A5o",
          "A4o",
          "A3o",
          "A2o",
          "KTo",
          "QJo",
          "QTo",
          "JTo",
          "T9s",
          "98s",
          "87s",
          "76s",
          "65s",
          "54s",
        ],
      ],
      [
        "C8",
        [
          "K9s",
          "K9o",
          "K8s",
          "K8o",
          "Q9s",
          "Q8s",
          "J9s",
          "T8s",
          "T9o",
          "97s",
          "98o",
          "86s",
          "87o",
          "75s",
          "76o",
          "64s",
        ],
      ],
    ];
    function parse(hand: string) {
      const i = RANK_NAMES.indexOf(hand.charAt(0));
      const j = RANK_NAMES.indexOf(hand.charAt(1));
      if (hand.length === 3 && hand.charAt(2) === "o") {
        return [j, i];
      }
      return [i, j];
    }
    const inRange: any = {};
    let hands: boolean[][] = Array(13);
    for (const i of Array(13).keys()) {
      hands[i] = Array(13);
      for (const j of Array(13).keys()) {
        hands[i][j] = false;
      }
    }
    for (const [name, handList] of HAND_CATEGORIES) {
      const h = hands.map((array: boolean[]) => [...array]);
      for (const [i, j] of handList.map(parse)) {
        h[i][j] = true;
      }
      inRange[name] = h;
      hands = h;
    }

    const ratios: any = {};
    for (const key in inRange) {
      let count = 0;
      for (const i of Array(13).keys()) {
        for (const j of Array(13).keys()) {
          if (inRange[key][i][j]) {
            if (i === j) {
              count += 6;
            } else if (i < j) {
              count += 4;
            } else {
              count += 12;
            }
          }
        }
      }
      ratios[key] = count / 1326.0;
    }

    return {
      range: "C1",
      ranges: HAND_CATEGORIES.map(([name, _]) => name),
      handNames,
      inRange,
      ratios,
    };
  },
  computed: {
    ratio: function () {
      return this.ratios[this.range];
    },
  },
});
</script>

<style scoped>
</style>
