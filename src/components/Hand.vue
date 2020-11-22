<template>
  <div
    class="flex rounded justify-center items-center h-8 w-8 font-semibold"
    :class="{
      'bg-green-300': inRange,
      'bg-white': !inRange,
    }"
  >
    {{ handName }}
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import VueTypes from "vue-types";

export default defineComponent({
  name: "Hand",
  props: {
    handName: VueTypes.string.isRequired,
    inRange: VueTypes.bool.def(false),
  },
});
</script>

<style scoped>
</style>
