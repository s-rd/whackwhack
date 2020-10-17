<template>
  <header
    class="header"
    :class="isPaused && 'header--paused'"
  >
    <div class="header__info">
      <h1 class="header__score">
        <span>Score</span>
        {{ score.current }}
      </h1>
      <h2 class="header__timer">{{ highScore }}</h2>
    </div>
    <div class="header__menu">
      <button
        @click="handleButtonClick"
        class="header__button header__button--pause"
      >
        <span v-if="isPaused">Play</span>
        <span v-else>Pause</span>
      </button>
    </div>
  </header>
</template>

<script>
export default {
  name: 'AppHeader',
  props: {
    score: {
      type: Object,
      required: true,
    },
    isPaused: {
      type: Boolean,
      required: true,
    },
    isStarted: {
      type: Boolean,
      required: true,
    },
  },
  methods: {
    handleButtonClick() {
      this.$emit('togglePause')
    },
  },
  computed: {
    highScore() {
      return this.score.high > this.score.current
        ? this.score.high
        : this.score.current
    },
  }
}
</script>
