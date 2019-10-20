<template>
  <div class="result">
    <div class="desc">{{title}}</div>
    <div :class="numberClass" class="number" :style="color">{{number}}</div>
    <span v-if="unit" class="pill">{{unit}}</span>
  </div>
</template>

<script>
export default {
  name: "Result",
  props: ["title", "number", "unit", "numberClass", "distanceUnit"],
  computed: {
    color() {
      if (this.numberClass == "time") return null;
      let hue = this.getTween(
        this.number,
        0,
        100,
        this.distanceUnit == "M" ? 2000.000064 : 6561.68
      );
      return `background-color: hsl(${hue < 336 ? hue : 335}, 100%, 61%);`;
    }
  },
  methods: {
    getTween(n, b, e, m) {
      return b + (n / m) * (e - b);
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
