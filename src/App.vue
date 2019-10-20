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
      :distanceUnit="unit"
      numberClass="distance"
    />
    <result
      :title="lang == 'en' ? 'Time since Lightning' : 'זמן מאז הברק'"
      :number="(time.sinceLightning / 1000).toFixed(2)"
      :unit="lang == 'en' ? 'seconds' : 'שניות'"
      numberClass="time"
    />
    <div class="py-5">
      <button @click="handleClick" class="btn btn-cta my-12">{{btnText[state][lang]}}</button>
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
    <div class="settings flex justify-center text-gray-500 mb-5">
      עברית
      <i-switch @change="toggleLang" :is-on="lang == 'he'" class="mx-2" />English
    </div>
    <a href="https://github.com/Yiddishe-Kop/lightning-distance" target="_blank">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        class="github mx-auto mt-12 opacity-25 hover:opacity-100"
      >
        <path
          d="M11.135,1.665A9.733,9.733,0,0,0,8.06,20.632c.486.09.664-.211.664-.468,0-.231-.008-.843-.013-1.656C6,19.1,5.432,17.2,5.432,17.2a2.579,2.579,0,0,0-1.08-1.424c-.884-.6.066-.591.066-.591a2.043,2.043,0,0,1,1.491,1A2.072,2.072,0,0,0,8.741,17a2.082,2.082,0,0,1,.618-1.3c-2.161-.246-4.433-1.081-4.433-4.81a3.763,3.763,0,0,1,1-2.612,3.5,3.5,0,0,1,.1-2.575s.817-.262,2.676,1a9.239,9.239,0,0,1,4.872,0c1.858-1.259,2.674-1,2.674-1a3.491,3.491,0,0,1,.1,2.575,3.757,3.757,0,0,1,1,2.612c0,3.738-2.275,4.561-4.443,4.8a2.326,2.326,0,0,1,.66,1.8c0,1.3-.012,2.35-.012,2.67,0,.26.175.563.67.467A9.734,9.734,0,0,0,11.135,1.665Z"
          transform="translate(-1.404 -1.665)"
        />
      </svg>
    </a>
  </div>
</template>

<script>
import Logo from "./components/Logo.vue";
import Alert from "./components/Alert.vue";
import Result from "./components/Result.vue";
import iSwitch from "./components/Switch.vue";

// metres/second
// const SPEED_OF_LIGHT = 299792458;
const SPEED_OF_SOUND = {
  M: 344,
  F: 1129
};

const STATE = {
  IDLE: 0,
  RUNNING: 1,
  RESULT: 2
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
      state: STATE.IDLE, // idle | timing | result
      btnText: [
        {
          en: "⚡️ Lightning! ⚡️",
          he: "⚡️ ברק! ⚡️"
        },
        {
          en: "💥 Thunder! 💥",
          he: "💥 רעם! 💥"
        },
        {
          en: "Reset",
          he: "איפוס"
        }
      ],
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
      this.state = STATE.RUNNING;
      this.time.lightning = Date.now();
      this.time.intervalId = setInterval(() => {
        this.time.sinceLightning = Date.now() - this.time.lightning;
      }, UPDATE_INTERVAL);
    },
    stopTimer() {
      this.time.thunder = Date.now();
      clearInterval(this.time.intervalId);
      this.state = STATE.RESULT;
    },
    reset() {
      this.state = STATE.IDLE;
      this.time = {
        lightning: 0,
        sinceLightning: 0,
        thunder: 0,
        intervalId: undefined
      };
    },
    handleClick() {
      switch (this.state) {
        case STATE.IDLE:
          this.startTimer();
          break;
        case STATE.RUNNING:
          this.stopTimer();
          break;
        case STATE.RESULT:
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

svg.github {
  width: 50px;
  fill: rgb(190, 211, 224);
  transition: all 0.2s ease-in;
}
</style>
