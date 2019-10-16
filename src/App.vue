<template>
  <div id="app" :dir="lang == 'en' ? 'ltr' : 'rtl'">
    <logo />
    <alert
      :pill="lang == 'en' ? 'life saving' : 'מציל חיים'"
    >{{lang == 'en' ? 'Calculate your distance from the lightning' : 'חשב את המרחק שלך מהברקים' }}</alert>
    <result
      :title="lang == 'en' ? 'Distance to Lightning' : 'מרחק לברקים'"
      :number="(distanceToLightning / 1000).toFixed(0)"
      :unit="unit == 'M' ? (lang == 'en' ? 'metres' : 'מטר') : (lang == 'en' ? 'feet' : 'רגל')"
      numberClass="distance"
    />
    <result
      :title="lang == 'en' ? 'Time since Lightning' : 'זמן מאז הברק'"
      :number="(time.sinceLightning / 1000).toFixed(2)"
      :unit="lang == 'en' ? 'seconds' : 'שניות'"
      numberClass="time"
    />
    <div class="py-5">
      <button @click="handleClick" class="btn btn-cta">{{btnText[state][lang]}}</button>
    </div>
    <alert :pill="lang == 'en' ? 'usage' : 'הוראות'">
      <ol class="list-decimal ml-3">
        <li>
          {{lang == 'en' ? 'Click the big button as soon as you see the' : 'לחץ על הכפתור הגדול ברגע שאתה רואה את '}}
          <strong>{{lang == 'en' ? 'Lightning' : 'הברק'}}</strong>.
        </li>
        <li>
          {{lang == 'en' ? 'Click the big button again as soon as you hear the' : 'לחץ שוב על הכפתור הגדול ברגע שאתה שומע את ' }}
          <strong>{{lang == 'en' ? 'Thunder' : 'הרעם' }}</strong>.
        </li>
        <li>{{lang == 'en' ? 'See results!' : 'קבל תוצאה!' }}</li>
      </ol>
    </alert>
    <div class="settings flex justify-center text-gray-500 mb-4">
      {{lang == 'en' ? 'metres' : 'מטר' }}
      <i-switch @change="toggleUnit" :is-on="unit == 'M'" class="mx-2" />
      {{lang == 'en' ? 'feet' : 'רגל' }}
    </div>
    <div class="settings flex justify-center text-gray-500">
      עברית
      <i-switch @change="toggleLang" :is-on="lang == 'he'" class="mx-2" />English
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

const UPDATE_INTERVAL = 30;

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
        idle: {
          en: "⚡️ Lightning! ⚡️",
          he: "⚡️ ברק! ⚡️"
        },
        timing: {
          en: "💥 Thunder! 💥",
          he: "💥 רעם! 💥"
        },
        result: {
          en: "Reset",
          he: "איפוס"
        }
      },
      time: {
        lightning: 0,
        sinceLightning: 0,
        thunder: 0,
        intervalId: undefined
      },
      unit: "M",
      lang: "he"
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
      this.time.lightning = Date.now();
      this.time.intervalId = setInterval(() => {
        this.time.sinceLightning = Date.now() - this.time.lightning;
      }, UPDATE_INTERVAL);
    },
    stopTimer() {
      this.time.thunder = Date.now();
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
    },
    toggleLang() {
      if (this.lang == "en") {
        this.lang = "he";
      } else {
        this.lang = "en";
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
