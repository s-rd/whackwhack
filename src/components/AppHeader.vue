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
      <h2 v-if="highScore" class="header__timer">{{ highScore }}</h2>
    </div>
    <h3 class="header__lvl">
      Lvl {{ level }} — {{ time }}
    </h3>
    <div class="header__menu">
      <button
        @click="handleQuitButtonClick"
        class="button header__button header__button--pause"
      >
        Quit
      </button>
    </div>
  </header>
</template>

<script>
import pad from 'pad-number'

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
    level: {
      type: Number,
      required: true,
    },
  },
  methods: {
    handleQuitButtonClick() {
      this.$emit('quit')
    },
  },
  computed: {
    highScore() {
      if (this.score.high === 0) return false
      return this.score.high > this.score.current
        ? this.score.high
        : this.score.current
    },
    time() {
      const mins = Math.floor(this.score.timeLapsed / 60)
      const seconds = this.score.timeLapsed - (mins * 60)
      return `${pad(mins, 2)}:${pad(seconds, 2)}`
    },
  }
}
</script>
