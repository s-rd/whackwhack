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
        <Slab
          v-for="(slab, i) in slabs"
          :data="slab"
          :key="i"
          :ref="`slab-${slab.i}`"
          @hit="handleButtonClick"
          @fail="handleGameOver"
        />
      </ul>
    </main>

    <TitleScreen
      :score="score"
      :is-paused="isPaused"
      :is-started="isStarted"
      :is-game-over="isGameOver"
      @togglePause="togglePause"
      @startOver="startOver"
    />

  </div>
</template>

<script>
import AppHeader from '@/components/AppHeader'
import TitleScreen from '@/components/TitleScreen'
import Slab from '@/components/Slab'

export default {
  name: 'App',
  components: {
    AppHeader,
    TitleScreen,
    Slab,
  },
  data() {
    return {
      isPaused: true,
      isStarted: false,
      isGameOver: false,
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
      moleTimer: null,
      slabs: [],
    }
  },
  mounted() {
    this.createSlabs()
    this.initListeners()
    this.getViewportSize()
  },
  beforeDestroy() {
    this.destroyListeners()
  },
  methods: {
    // Game timeline
    startGame() {
      this.isStarted = true
      this.isPaused = false
      this.startTimer()
      this.startMoleTimer()
    },
    startTimer() {
      this.timer = setInterval(() => {
        this.score.timeLapsed += 1
      }, 1000)
    },
    startMoleTimer() {
      // Initiate first mole slab
      const slab = this.getRandomSlab()
      slab.isMoled = true

      const setNextTimeout = () => {
        // Time depends on difficulty level
        const interval = Math.round(1000 * (3 / this.level))

        this.moleTimer = setTimeout(() => {
          const slab = this.getRandomSlab()
          slab.isMoled = true
          this.$refs[`slab-${slab.i}`][0].startTimer()
          setNextTimeout()
        }, interval)
      }
      setNextTimeout()
    },
    togglePause() {
      if (!this.isStarted) {
        this.startGame()
      } else {
        this.isPaused = !this.isPaused
        if (this.isPaused) {
          clearInterval(this.timer)
          clearTimeout(this.moleTimer)
          this.timer = null
          this.moleTimer = null
        } else {
          this.startTimer()
          this.startMoleTimer()
        }
      }
    },
    startOver() {
      // Reset game if previously played
      this.createSlabs()
      this.score.current = 0
      this.isGameOver = false
      this.isPaused = false
      this.isStarted = true
      this.startTimer()
      this.startMoleTimer()
    },

    // Slab helpers
    getRandomSlab() {
      const [min, max] = [0, 20]
      const i = Math.floor(Math.random() * (max - min)) + min
      return this.slabs[i]
    },
    createSlabs() {
      let slabs = []
      for (let i = 0; i < 20; i++) {
        slabs.push({ i, isMoled: false })
      }
      this.slabs = slabs
    },

    // Listeners and handlers
    initListeners() {
      window.addEventListener('mousemove', this.handleMousemove)
      window.addEventListener('resize', this.getViewportSize)
    },
    destroyListeners() {
      window.removeEventListener('mousemove', this.handleMousemove)
      window.removeEventListener('resize', this.getViewportSize)
    },
    handleGameOver() {
      this.isGameOver = true
      this.isPaused = true
      this.isStarted = false

      // Set high score
      if (this.score.current > this.score.high) {
        this.score.high = this.score.current
      }

      // Stop/clear timers
      clearInterval(this.timer)
      clearTimeout(this.moleTimer)
      this.timer = null
      this.moleTimer = null
      this.score.timeLapsed = 1
    },
    handleButtonClick(i) {
      this.score.current += 1
      this.slabs[i].isMoled = false
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
      // Increase the level every 20 seconds
      return Math.ceil(this.score.timeLapsed / 20)
    },
  },
}
</script>
