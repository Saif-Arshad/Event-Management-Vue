<script setup>
import { ref, computed } from 'vue'
import Home from './components/Home.vue'
import LoginPage from './components/Login.vue'
import RegisterPage from './components/Register.vue'


const routes = {
  '/': Home,
  '/login': LoginPage,
  '/register': RegisterPage
}
const currentPath = ref(window.location.hash)
window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})
const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})

</script>

<template>
   <!-- <a href="#/">Home</a> |
  <a href="#/login">Login</a> | -->
  <component :is="currentView" />
</template>
