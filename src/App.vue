<template>
  <div id="app">

    <header class="header">
      <div class="header__info">
        <h1 class="header__score">0</h1>
        <h2 class="header__timer">0</h2>
      </div>
      <div class="header__menu">
        <button class="header__button header__button--pause">
          <transition name="swap" mode="in-out">
            ||
          </transition>
        </button>
      </div>
    </header>

    <div
      class="score"
      :style="scoreStyle"
    >
      <svg preserveAspectRatio="none" viewBox="0 0 88 76" fill="none" xmlns="http://www.w3.org/2000/svg">
        <g clip-path="url(#clip0)">
          <text fill="black" xml:space="preserve" style="white-space: pre" font-family="Inter" font-size="100" letter-spacing="-0.1em">
            <tspan x="-4" y="75.3636">17</tspan>
          </text>
        </g>
      </svg>
    </div>

    <main class="gamepad">
      <ul class="gamepad__surface">
        <li
          v-for="i in 20"
          class="gamepad__item"
          :key="i"
        >
          <button class="gamepad__button" :class="i == 7 && 'gamepad__button--active'"></button>
        </li>
      </ul>
    </main>

    <div class="pause-screen">
      pause screen
    </div>

  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      windowWidth: 0,
      windowHeight: 0,
    }
  },
  mounted() {
    this.setWindowDimensions()
    this.initListeners()
  },
  beforeDestroy() {
    this.destroyListeners()
  },
  methods: {
    initListeners() {
      window.addEventListener('resize', this.setWindowDimensions)
    },
    destroyListeners() {
      window.removeEventListener('resize', this.setWindowDimensions)
    },
    setWindowDimensions() {
      try {
        this.windowWidth = window.innerWidth
        this.windowHeight = window.innerHeight
      } catch(e) {
        console.error(e)
      }
    },
  },
  computed: {
    scoreStyle() {
      // const width = this.windowWidth
      return {
        // transform: 'scale(' + width + '%)',
      }
    },
  },
}
</script>
