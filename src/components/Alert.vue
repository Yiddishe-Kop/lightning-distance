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

<style>
</style>
