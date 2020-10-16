<template>
  <div id="app">

    <header class="header">
      <div class="header__info">
        <h1 class="header__score">0</h1>
        <h2 class="header__timer">0</h2>
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
      <ul class="gamepad__surface">
        <li
          v-for="i in 20"
          class="gamepad__item"
          :key="i"
        >
          <button class="gamepad__button">Mole</button>
        </li>
      </ul>
    </main>

    <div class="pause-screen">
      pause screen
    </div>

  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {

    }
  }
}
</script>
