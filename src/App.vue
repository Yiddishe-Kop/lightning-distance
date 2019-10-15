<template>
  <div id="app">
    <logo />
    <alert pill="life saving">Calculate your distance from the lightning</alert>
    <result
      title="Distance to Lightning"
      :number="distanceToLightning"
      :unit="unit == 'M' ? 'metres' : 'feet'"
      numberClass="distance"
    />
    <result
      title="Time since Lightning"
      :number="time.sinceLightning"
      unit="seconds"
      numberClass="time"
    />
    <div class="py-5">
      <button @click="handleClick" class="btn btn-cta">{{btnText[state]}}</button>
    </div>
    <alert pill="usage">
      <ol class="list-decimal ml-3">
        <li>
          Click the big button as soon as you see the
          <strong>Lightning</strong>.
        </li>
        <li>
          Click the big button again as soon as you hear the
          <strong>Thunder</strong>.
        </li>
        <li>See results!</li>
      </ol>
    </alert>
    <div class="settings flex justify-center text-gray-500">
      metres
      <i-switch @change="toggleUnit" :is-on="unit == 'M'" class="mx-2" />feet
    </div>
  </div>
</template>

<script>
import Logo from "./components/Logo.vue";
import Alert from "./components/Alert.vue";
import Result from "./components/Result.vue";
import iSwitch from "./components/Switch.vue";

// metres/second
const SPEED_OF_LIGHT = 299792458;
const SPEED_OF_SOUND = {
  M: 344,
  F: 1129
};

const UPDATE_INTERVAL = 12;

export default {
  name: "app",
  components: {
    Logo,
    Alert,
    Result,
    iSwitch
  },
  data() {
    return {
      state: "idle", // idle | timing | result
      btnText: {
        idle: "⚡️ Lightning! ⚡️",
        timing: "💥 Thunder! 💥",
        result: "Reset"
      },
      time: {
        lightning: 0,
        sinceLightning: 0,
        thunder: 0,
        intervalId: undefined
      },
      unit: "M"
    };
  },
  computed: {
    distanceToLightning() {
      if (!this.time.sinceLightning) return 0;
      return (this.time.sinceLightning * SPEED_OF_SOUND[this.unit]).toFixed(2);
    }
  },
  methods: {
    startTimer() {
      this.state = "timing";
      this.time.lightning = new Date();
      this.time.intervalId = setInterval(() => {
        this.time.sinceLightning = (
          (this.time.sinceLightning * 1000 + UPDATE_INTERVAL) /
          1000
        ).toFixed(2);
      }, UPDATE_INTERVAL);
    },
    stopTimer() {
      this.time.thunder = new Date();
      clearInterval(this.time.intervalId);
      this.state = "result";
    },
    reset() {
      this.state = "idle";
      this.time = {
        lightning: 0,
        sinceLightning: 0,
        thunder: 0,
        intervalId: undefined
      };
    },
    handleClick() {
      switch (this.state) {
        case "idle":
          this.startTimer();
          break;
        case "timing":
          this.stopTimer();
          break;
        case "result":
          this.reset();
          break;
      }
    },
    toggleUnit() {
      if (this.unit == "M") {
        this.unit = "F";
      } else {
        this.unit = "M";
      }
    }
  }
};
</script>

<style lang="scss">
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;

  ol {
    li {
      @apply mx-4;
      &:not(:last-child) {
        @apply mb-2;
      }
    }
  }
}
</style>
