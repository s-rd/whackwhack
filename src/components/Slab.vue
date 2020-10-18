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
      this.timer = setTimeout(() => {
        this.$emit('fail', this.data.i)
      }, 12000)

      this.$on('fail', () => {
        clearTimeout(this.timer)
        this.timer = null
      })
    },
    handleButtonClick() {
      // Give one point if this slab 'isMoled'
      if (this.data.isMoled) {
        clearTimeout(this.timer)
        this.$emit('hit', this.data.i)
        this.timer = null
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
