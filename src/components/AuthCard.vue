<template>
  <template v-if="!props.embedded">
    <div class="auth-overlay" @click.self="close">
      <div class="auth-card">
        <div class="tabs">
          <button :class="{active: localMode==='login'}" @click="localMode='login'">Login</button>
          <button :class="{active: localMode==='register'}" @click="localMode='register'">Register</button>
        </div>

        <h3 class="title">Gold & Silver Calculator</h3>

        <form @submit.prevent="submit">
          <div v-if="localMode==='register'" class="field">
            <label>Full name
              <input v-model="fullName" placeholder="Full name" />
            </label>
          </div>

          <div class="field">
            <label>Email
              <input v-model="email" placeholder="name@example.com" />
            </label>
          </div>

          <div class="field">
            <label>Password
              <input type="password" v-model="password" placeholder="" />
            </label>
          </div>

          <div v-if="localMode==='register'" class="field">
            <label>Confirm password
              <input type="password" v-model="confirmPassword" placeholder="" />
            </label>
          </div>

          <div class="actions-row">
            <button class="btn primary" type="submit">{{ localMode==='login' ? 'Login' : 'Register' }}</button>
            <button class="btn secondary" type="button" @click="useInternal">Use internal account</button>
          </div>

          <p class="tip">Tip: for quick access use the internal account email: daniel12@gmail.com</p>
        </form>

        <button class="close" @click="close">×</button>
      </div>
    </div>
  </template>

  <template v-else>
    <div class="auth-card embedded">
      <div class="tabs embedded-tabs">
        <button :class="{active: localMode==='login'}" @click="localMode='login'">Login</button>
        <button :class="{active: localMode==='register'}" @click="localMode='register'">Register</button>
      </div>

      <h3 class="title embedded-title">Welcome back</h3>
      <p class="subtitle">Manage prices, conversions and quick calculations</p>

      <form @submit.prevent="submit">
        <div v-if="localMode==='register'" class="field">
          <label>Full name
            <input v-model="fullName" placeholder="Full name" />
          </label>
        </div>

        <div class="field">
          <label>Email
            <input v-model="email" placeholder="name@example.com" />
          </label>
        </div>

        <div class="field">
          <label>Password
            <input type="password" v-model="password" placeholder="" />
          </label>
        </div>

        <div v-if="localMode==='register'" class="field">
          <label>Confirm password
            <input type="password" v-model="confirmPassword" placeholder="" />
          </label>
        </div>

        <div class="actions-row">
          <button class="btn primary large" type="submit">{{ localMode==='login' ? 'Login' : 'Register' }}</button>
          <button class="btn secondary" type="button" @click="useInternal">Use internal account</button>
        </div>

        <p class="tip">Tip: for quick access use the internal account email: daniel12@gmail.com</p>
      </form>
    </div>
  </template>
</template>

<script setup>
import { ref, watch } from 'vue'
const emit = defineEmits(['close', 'auth'])

const props = defineProps({ mode: { type: String, default: 'login' }, embedded: { type: Boolean, default: false } })
const localMode = ref(props.mode || 'login')
const fullName = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')

watch(() => props.mode, (v) => { localMode.value = v || 'login' })

const close = () => { if (!props.embedded) emit('close') }

const useInternal = () => {
  email.value = 'daniel12@gmail.com'
  password.value = 'password'
}

const submit = () => {
  if (!email.value || !password.value) {
    alert('Please enter email and password')
    return
  }
  if (localMode.value === 'register') {
    if (!fullName.value) { alert('Please enter your full name'); return }
    if (password.value !== confirmPassword.value) { alert('Passwords do not match'); return }
  }

  // emit mock auth event with payload (no backend)
  emit('auth', { mode: localMode.value, fullName: fullName.value, email: email.value })
  alert((localMode.value === 'login' ? 'Logged in' : 'Registered') + ' (mock)')
  close()
}
</script>

<style scoped>
.auth-overlay {
  position: fixed;
  inset: 0;
  background: rgba(6,20,40,0.25);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1500;
}
.auth-card {
  width: 420px;
  background: #fff;
  border-radius: 10px;
  padding: 1.1rem 1.25rem 1.4rem;
  position: relative;
  box-shadow: 0 10px 30px rgba(10,20,30,0.12);
}
.auth-card.embedded { box-shadow: none; width: 100%; padding: 1.25rem; }
.embedded-title { margin-top: 0.4rem; font-weight:700 }
.subtitle { margin: 0.25rem 0 0.8rem 0; color: #65748b; font-size:0.9rem }
.embedded-tabs { gap:6px; margin-bottom:0.3rem }
.tabs button { padding:8px 12px; border-radius: 999px; border:1px solid rgba(16,24,40,0.04) }
.tabs button.active { background: #4b6cff; color:#fff; border-color: transparent }
.btn.large { padding:10px 14px }
.tabs { display:flex; gap:8px; margin-bottom:0.6rem }
.tabs button { flex:1; padding:8px 10px; background:transparent; border:1px solid transparent; border-radius: 8px; cursor:pointer }
.tabs button.active { background: linear-gradient(90deg,#6b7bff,#4b6cff); color:#fff; border-color: #4960ff }
.title { font-size:1rem; margin:0 0 0.6rem 0 }
.field { margin-bottom:0.6rem }
.field input { width:100%; padding:8px 10px; border-radius:6px; border:1px solid #e6e6f0 }
.actions-row { display:flex; gap:0.5rem; margin-top:0.6rem }
.btn.primary { background:#4b6cff; color:#fff; border:none; padding:8px 12px; border-radius:8px; cursor:pointer }
.btn.secondary { background:transparent; border:1px solid rgba(75,108,255,0.15); color:#4b6cff; padding:8px 12px; border-radius:8px; cursor:pointer }
.tip { font-size:0.85rem; color:#777; margin-top:0.6rem }
.close { position:absolute; top:8px; right:10px; background:transparent; border:none; font-size:18px; cursor:pointer }
</style>
 
