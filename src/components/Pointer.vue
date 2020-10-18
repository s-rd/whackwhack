<template>
  <div
    class="pointer"
    :class="pointerClass"
    :style="pointerStyle"
  >
    <span></span>
  </div>
</template>

<script>
export default {
  name: 'Pointer',
  data() {
    return {
      isInView: true,
      pointer: {
        x: -100,
        y: -100,
      },
    }
  },
  mounted() {
    window.addEventListener('mousemove', this.handleMousemove)
    window.addEventListener('mouseout', this.handleMouseout)
  },
  methods: {
    handleMouseout(ev) {
      const from = ev.relatedTarget || ev.toElement
      if (!from || from.nodeName == 'HTML') {
        this.isInView = false
      }
    },
    handleMousemove(ev) {
      if (!this.isInView) this.isInView = true
      try {
        this.pointer = {
          x: ev.pageX,
          y: ev.pageY,
        }
      } catch(e) {
        console.error(e)
      }
    },
  },
  computed: {
    pointerClass() {
      return {
        'pointer--out': !this.isInView,
      }
    },
    pointerStyle() {
      return {
        top: `${this.pointer.y}px`,
        left: `${this.pointer.x}px`,
      }
    },
  },
}
</script>
