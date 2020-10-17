<template>
  <div id="app">

    <AppHeader
      :score="score"
      :is-paused="isPaused"
      :is-started="isStarted"
      :level="level"
      @togglePause="togglePause"
    />

    <main
      class="gamepad"
      :class="isPaused && 'gamepad--paused'"
    >
      <ul
        class="gamepad__surface"
        :style="gamepadTiltStyle"
      >
        <li
          v-for="i in 20"
          class="gamepad__item"
          :key="i"
        >
          <button
            class="gamepad__button"
            @click="handleButtonClick"
          ></button>
        </li>
      </ul>
    </main>

    <TitleScreen
      :is-paused="isPaused"
      :is-started="isStarted"
      @togglePause="togglePause"
    />

  </div>
</template>

<script>
import AppHeader from '@/components/AppHeader'
import TitleScreen from '@/components/TitleScreen'

export default {
  name: 'App',
  components: {
    AppHeader,
    TitleScreen,
  },
  data() {
    return {
      isPaused: true,
      isStarted: false,
      score: {
        current: 0,
        high: 0,
        timeLapsed: 1,
      },
      pointer: {
        x: 0,
        y: 0,
      },
      viewport: {
        width: 0,
        height: 0,
      },
      timer: null,
    }
  },
  mounted() {
    this.initListeners()
    this.getViewportSize()
  },
  beforeDestroy() {
    this.destroyListeners()
  },
  methods: {
    startGame() {
      this.isStarted = true
      this.isPaused = false
      this.startTimer()
    },
    startTimer() {
      this.timer = setInterval(() => {
        this.score.timeLapsed += 1
      }, 1000)
    },
    initListeners() {
      window.addEventListener('mousemove', this.handleMousemove)
      window.addEventListener('resize', this.getViewportSize)
    },
    destroyListeners() {
      window.removeEventListener('mousemove', this.handleMousemove)
      window.removeEventListener('resize', this.getViewportSize)
    },
    togglePause() {
      if (!this.isStarted) {
        this.startGame()
      } else {
        this.isPaused = !this.isPaused
        if (this.isPaused) {
          clearInterval(this.timer)
          this.timer = null
        } else {
          this.startTimer()
        }
      }
    },
    handleButtonClick() {
      this.score.current += 1
    },
    handleMousemove(ev) {
      try {
        this.pointer = {
          x: ev.pageX,
          y: ev.pageY,
        }
      } catch(e) {
        console.error(e)
      }
    },
    getViewportSize() {
      try {
        this.viewport = {
          width: window.innerWidth,
          height: window.innerHeight,
        }
      } catch(e) {
        console.error(e)
      }
    },
  },
  computed: {
    gamepadTiltStyle() {
      let tiltX, tiltY

      const { width, height } = this.viewport // Viewport size
      const { y, x } = this.pointer // Pointer location

      const [ tiltMinDegX, tiltMaxDegX ] = [5, -5]
      const [ tiltMinDegY, tiltMaxDegY ] = [5, 15]

      tiltX = tiltMinDegX + ((x / width) * (tiltMaxDegX-tiltMinDegX))
      tiltY = tiltMinDegY + ((y / height) * (tiltMaxDegY-tiltMinDegY))

      return {
        transform: `rotateY(${tiltX}deg) rotateX(${tiltY}deg)`,
      }
    },
    level() {
      return Math.ceil(this.score.timeLapsed / 30)
    },
  },
}
</script>
