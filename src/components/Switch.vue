<template>
  <span
    @click="$emit('change')"
    :class="{'justify-start': isOn, 'justify-end bg-green-500': !isOn}"
    class="switch"
  >
    <span class="switch-knob"></span>
  </span>
</template>

<script>
export default {
  name: "iSwitch",
  props: ["isOn"]
};
</script>

<style lang="scss">
.switch {
  @apply border rounded-full border-gray-500 flex items-center cursor-pointer w-12;
  .switch-knob {
    @apply rounded-full border w-6 h-6 border-gray-500 shadow-inner bg-white shadow;
  }
}
</style>
