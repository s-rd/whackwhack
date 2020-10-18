<template>
  <li class="gamepad__slab">
    <button
      class="gamepad__button"
      :class="slabClass"
      @click="handleButtonClick"
    ></button>
  </li>
</template>

<script>
export default {
  name: 'Slab',
  props: {
    data: {
      type: Object,
      required: true,
    },
    level: {
      type: Number,
      required: false,
      default: 1,
    },
  },
  data() {
    return {
      timer: null,
    }
  },
  methods: {
    startTimer() {
      // Start timer
      // Not clicking the slab for X seconds makes you lose the game
      // Time decreases by 22% at each difficulty level increase
      const decreaseFactor = 0.22
      const initMs = 9000
      const minMs = 5000
      const interval = Math.max(0, (initMs - (initMs * decreaseFactor * this.level))) + minMs

      console.log(`Slab ${this.data.i}: Starting timer`, interval)

      this.timer = setTimeout(() => {
        this.$emit('fail', this.data.i)
        this.$root.$emit('stop-slab-timers')
        console.log(`Slab ${this.data.i}: Reporting game over`)
      }, interval)

      // If any other slabs 'fail', stop this one too
      this.$root.$on('stop-slab-timers', () => {
        this.stopTimer()
      })
    },
    stopTimer() {
      clearTimeout(this.timer)
      this.timer = null
    },
    handleButtonClick() {
      // Give one point if this slab 'isMoled'
      if (this.data.isMoled) {
        this.$emit('hit', this.data.i)
        this.stopTimer()
        console.log(`Slab ${this.data.i}: Hit (stopped timer)`)
      }
    },
  },
  computed: {
    slabClass() {
      return {
        'gamepad__button--moled': this.data.isMoled,
      }
    },
  },
}
</script>
