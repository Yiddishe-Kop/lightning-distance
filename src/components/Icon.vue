<template>
  <svg
    class="fill-current opacity-75 h-4 w-4"
    xmlns="http://www.w3.org/2000/svg"
    viewBox="0 0 20 20"
  >
    <path :d="icons[icon]" />
  </svg>
</template>

<script>
let icons = {
  arrow:
    "M12.95 10.707l.707-.707L8 4.343 6.586 5.757 10.828 10l-4.242 4.243L8 15.657l4.95-4.95z"
};

export default {
  name: "Icon",
  props: ["icon"],
  data() {
    return {
      icons: icons
    };
  }
};
</script>

<style>
</style>
