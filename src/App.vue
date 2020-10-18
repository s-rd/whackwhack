<template>
  <div id="app">

    <Pointer/>

    <AppHeader
      :score="score"
      :is-paused="isPaused"
      :is-started="isStarted"
      :level="level"
      @quit="handleQuit"
    />

    <TitleScreen
      :score="score"
      :is-paused="isPaused"
      :is-started="isStarted"
      :is-game-over="isGameOver"
      @start="startGame"
      @startOver="resetGame"
    />

    <!-- Main Gamepad -->
    <main
      class="gamepad"
      :class="gamepadClass"
    >
      <ul
        class="gamepad__surface"
        :style="gamepadTiltStyle"
      >
        <Slab
          v-for="(slab, i) in slabs"
          :data="slab"
          :level="level"
          :key="i"
          :ref="`slab-${slab.i}`"
          @hit="handleMoleClick"
          @fail="handleGameOver"
        />
      </ul>
    </main>

    <AppFooter v-if="!isPaused"/>

  </div>
</template>

<script>
import Pointer from '@/components/Pointer'
import AppHeader from '@/components/AppHeader'
import AppFooter from '@/components/AppFooter'
import TitleScreen from '@/components/TitleScreen'
import Slab from '@/components/Slab'

export default {
  name: 'App',
  components: {
    Pointer,
    AppHeader,
    AppFooter,
    TitleScreen,
    Slab,
  },
  data() {
    return {
      // State
      isPaused: true,
      isStarted: false,
      isGameOver: false,

      // Timers
      timer: null,
      moleTimer: null,
      slabs: [],

      // Score
      score: {
        current: 0,
        high: 0,
        timeLapsed: 1,
      },

      // Visual styling
      pointer: {
        x: 0,
        y: 0,
      },
      viewport: {
        width: 0,
        height: 0,
      },
    }
  },
  mounted() {
    this.createSlabs()
    this.initListeners()
    this.getViewportSize()

    // Set high score from localstorage if there is one
    if (localStorage.getItem('highscore')) {
      this.score.high = localStorage.getItem('highscore')
    }
  },
  beforeDestroy() {
    this.destroyListeners()
  },
  watch: {
    level() {
      // Emit level up message when it increases
      this.$root.$emit('footer', ['Level up!'])
    },
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
    resetGame() {
      // Reset game if previously played
      this.createSlabs()
      this.score.current = 0
      this.isGameOver = false
      this.isPaused = false
      this.isStarted = true
      this.startTimer()
      this.startMoleTimer()
    },
    stopTimers() {
      // Stop/clear timers
      clearInterval(this.timer)
      clearTimeout(this.moleTimer)
      this.timer = null
      this.moleTimer = null
      this.score.timeLapsed = 1
      this.$root.$emit('stop-slab-timers')
    },
    setHighScore() {
      if (this.score.current > this.score.high) {
        this.score.high = this.score.current
        localStorage.setItem('highscore', this.score.current)
      }
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
    handleQuit() {
      this.isStarted = false
      this.isPaused = true
      this.isGameOver = false
      this.score.current = 0

      this.createSlabs()
      this.stopTimers()
    },
    handleGameOver() {
      this.isGameOver = true
      this.isPaused = true
      this.isStarted = false

      // Set high score
      this.setHighScore()
      // Stop/clear timers
      this.stopTimers()
    },
    handleMoleClick(i) {
      this.score.current += 1
      this.slabs[i].isMoled = false

      this.$root.$emit('footer', ['+1', this.score.current])
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

    // Visual styling
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
    gamepadClass() {
      return {
        'gamepad--paused': this.isPaused,
        [`gamepad--level-${this.level}`]: true,
      }
    },
    level() {
      // Increase the level every 20 seconds
      return Math.ceil(this.score.timeLapsed / 15)
    },
  },
}
</script>
