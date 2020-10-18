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
  },
  data() {
    return {
      timer: null,
    }
  },
  methods: {
    startTimer() {
      // Not clicking the slab for 12 seconds makes you lose the game
      console.log('timer started', this.data.i)
      this.timer = setTimeout(() => {
        console.log('fail', this.data.i)
        this.$emit('fail', this.data.i)
        this.$root.$emit('stop-slab-timers')
      }, 12000)

      // If any other slabs 'fail', stop this one too
      this.$root.$on('stop-slab-timers', () => {
        if (this.timer !== null && this.data.isMoled) this.stopTimer()
      })
    },
    stopTimer() {
      console.log('timer stopped', this.data.i)
      clearTimeout(this.timer)
      this.timer = null
    },
    handleButtonClick() {
      // Give one point if this slab 'isMoled'
      if (this.data.isMoled) {
        this.$emit('hit', this.data.i)
        this.stopTimer()
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
