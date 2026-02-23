<script setup>
import { ref } from 'vue'
import GoldCalculator from './components/GoldCalculator.vue'
import AuthCard from './components/AuthCard.vue'

const isAuthenticated = ref(false)
const user = ref(null)
const authMode = ref('login')

const onAuth = (payload) => {
  isAuthenticated.value = true
  user.value = payload
}

const logout = () => {
  isAuthenticated.value = false
  user.value = null
}
</script>

<template>
  <div id="app">
    <header class="page-header">
      <h1>Gold &amp; Silver Price Calculator</h1>
    </header>

    <main>
      <section class="panel">
        <div class="panel-inner">
          <AuthCard v-if="!isAuthenticated" :mode="authMode" :embedded="true" @auth="onAuth" />
          <GoldCalculator v-else />
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

main {
  display: block;
  max-width: 1100px;
  margin: 0 auto;
}

.page-header {
  display: flex;
  justify-content: center;
  align-items: center;
  max-width: 1100px;
  margin: 0 auto 1.25rem;
}

.page-header h1 {
  font-size: 1.25rem;
  margin: 0;
  text-align: center;
}

.btn {
  background: #4b6cff;
  color: #fff;
  border: none;
  padding: 6px 10px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.85rem;
}

.btn.outline {
  background: transparent;
  color: #4b6cff;
  border: 1px solid rgba(75,108,255,0.15);
}

.panel {
  background: var(--color-background-soft);
  border-radius: 12px;
  padding: 2rem;
  box-shadow: 0 6px 30px rgba(10,20,30,0.06);
}

.panel-inner {
  max-width: 740px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (max-width: 640px) {
  .panel { padding: 1rem }
  .panel-inner { width: 100% }
}

@media (min-width: 1024px) {
  body {
    display: flex;
    place-items: center;
  }

  #app {
    display: grid;
    grid-template-columns: 1fr;
    padding: 0 2rem;
  }
}
</style>
