<template>
  <div class="alert">
    <div class="alert-inner" role="alert">
      <span v-if="pill" class="pill">{{pill}}</span>
      <span class="alert-text">
        <slot />
      </span>
      <icon v-if="icon" :icon="icon" />
    </div>
  </div>
</template>

<script>
import Icon from "./Icon.vue";

export default {
  name: "Alert",
  props: ["pill", "icon"],
  components: {
    Icon
  }
};
</script>

<style lang="scss">
.alert {
  @apply mb-5 p-1 bg-pink-400 items-center justify-center text-pink-100 leading-none rounded-full inline-flex;
  .alert-inner {
    @apply p-2 bg-pink-200 items-center text-pink-600 leading-none rounded-full inline-flex;
    .pill {
      @apply flex rounded-full bg-pink-500 text-pink-100 uppercase px-2 py-1 text-xs font-bold mr-3;
    }
    .alert-text {
      @apply font-semibold mr-2 flex-auto;
      text-align: start;
    }
  }
}
</style>
