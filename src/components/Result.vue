<template>
  <div class="result">
    <div class="desc">{{title}}</div>
    <div :class="[numberClass, color]" class="number">{{number}}</div>
    <span v-if="unit" class="pill">{{unit}}</span>
  </div>
</template>

<script>
export default {
  name: "Result",
  props: ["title", "number", "unit", "numberClass"],
  computed: {
    color() {
      if (this.numberClass == "time") return null;
      let color = "red-600";
      if (this.number < 50) {
        color = "red-600";
      } else if (this.number < 150) {
        color = "red-300";
      } else if (this.number < 250) {
        color = "green-300";
      } else if (this.number < 400) {
        color = "green-400";
      } else {
        color = "green-500";
      }
      return `bg-${color}`;
    }
  }
};
</script>

<style lang="scss">
.result {
  @apply mt-5 mx-auto;
  max-width: 280px;
  .desc {
    @apply bg-blue-500 text-blue-100 font-semibold inline-block px-2 py-1 text-sm rounded-t-lg;
  }
  .number {
    @apply rounded-lg pb-2 shadow-inner;
    &.distance {
      @apply text-gray-800 text-6xl font-black;
    }
    &.time {
      @apply bg-gray-200 text-gray-700 text-5xl font-bold;
    }
  }
  .pill {
    @apply -mt-2 relative tracking-wide;
    top: -1em;
  }
}
.pill {
  @apply inline-flex rounded-full bg-blue-400 text-blue-900 uppercase px-2 py-1 text-xs font-bold;
}
</style>
