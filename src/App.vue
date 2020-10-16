<template>
  <div id="app">

    <header class="header">
      <div class="header__scores">
        0
      </div>
      <div class="header__menu">
        <button class="header__button header__button--pause">
          <transition name="swap" mode="in-out">
            ||
          </transition>
        </button>
      </div>
    </header>

    <main class="gamepad">
      gamepad
    </main>

    <div class="pause-screen">
      pause screen
    </div>

  </div>
</template>

<script>
export default {
  name: 'App',
  components: { }
}
</script>
