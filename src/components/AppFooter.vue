<template>
  <footer class="footer">
    <ul class="footer__list">
      <li
        v-for="(ev, i) in events"
        class="footer__list-item"
        :key="i"
      >
        <span>{{ ev }}</span>
      </li>
    </ul>
  </footer>
</template>

<script>
export default {
  name: 'AppFooter',
  data() {
    return {
      events: [],
    }
  },
  mounted() {
    this.$root.$on('footer', ev => {
      if (ev[1] && ev[1] % 5 == 0) {
        const ddrWords = ['Awesome!', 'Good!', 'Savage!', 'On fire!', 'Combo!']
        const randomWord = ddrWords[Math.floor(Math.random() * ddrWords.length)];
        this.addEvent(randomWord)
      } else {
        this.addEvent(ev[0])
      }
    })
  },
  methods: {
    addEvent(ev) {
      this.events.push(ev)
    },
  },
}
</script>
