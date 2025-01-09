<script setup>
import { ref, computed } from 'vue';
import Home from './components/Pages/Home.vue';
import LoginPage from './components/Pages/Login.vue';
import Header from './components/Header.vue';
import RegisterPage from './components/Pages/Register.vue';
import AccountPage from './components/Pages/Account.vue';
import EventDetail from './components/Pages/EventDetail.vue'; // Import your EventDetail component

const routes = {
  '/': Home,
  '/login': LoginPage,
  '/register': RegisterPage,
  '/account': AccountPage,
  '/events/:id': EventDetail, 
};

const currentPath = ref(window.location.hash.slice(1) || '/');

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash.slice(1) || '/';
});

const currentView = computed(() => {
  const path = currentPath.value;

  if (routes[path]) {
    return routes[path];
  }

  const dynamicRoute = Object.keys(routes).find(route => {
    const routeParts = route.split('/');
    const pathParts = path.split('/');
    if (routeParts.length !== pathParts.length) return false;

    return routeParts.every((part, index) => {
      return part.startsWith(':') || part === pathParts[index];
    });
  });

  return routes[dynamicRoute] || NotFound;
});

const headerPages = ['/'];

const isShowHeader = computed(() => {
  return headerPages.includes(currentPath.value.split('/')[1] || '/');
});
</script>

<template>
  <Header v-if="isShowHeader" />
  <component :is="currentView" />
</template>
