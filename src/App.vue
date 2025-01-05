<script setup>
import { ref, computed } from 'vue'
import Home from './components/Pages/Home.vue'
import LoginPage from './components/Pages/Login.vue'
import Header from './components/Header.vue'
import RegisterPage from './components/Pages/Register.vue'

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

const headerPages = ['/',]

const isShowHeader = computed(() => {
  return headerPages.includes(currentPath.value.slice(1) || '/')
})
</script>

<template>
   <Header v-if="isShowHeader" />
  <component :is="currentView" />
</template>
