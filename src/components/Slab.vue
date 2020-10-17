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
  methods: {
    handleButtonClick() {
      // Trigger one point if slab has a mole
      if (this.data.isMoled) {
        this.$emit('hit', this.data.i)
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
