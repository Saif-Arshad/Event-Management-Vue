<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

// State to track scrolling
const isScrolled = ref(false);

// State to check if the user is logged in
const isLoggedIn = ref(false);

const handleScroll = () => {
  isScrolled.value = window.scrollY > 0;
};

// Check for authToken in localStorage
const checkAuthToken = () => {
  isLoggedIn.value = !!localStorage.getItem('authToken');
};

// Add/remove event listeners for scroll and check auth token on mount
onMounted(() => {
  window.addEventListener('scroll', handleScroll);
  checkAuthToken();
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});
</script>

<template>
  <div
    :class="[ 
      'fixed top-0 left-0 z-50 flex flex-wrap items-center justify-between gap-5 w-full p-3 px-5 transition-all duration-300',
      isScrolled ? 'bg-white shadow-lg' : 'bg-transparent',
    ]"
  >
    <div class="flex items-center space-x-2">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="2"
        stroke="currentColor"
        :class="[isScrolled ? 'text-black' : 'text-white']"
        class="w-6 h-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M3 10h11M9 21V3M17 14l4-4m0 0l-4-4m4 4H3"
        />
      </svg>
      <!-- Dynamic Title -->
      <h2 
        :class="[ 
          'font-bold text-xl',
          isScrolled ? 'text-black' : 'text-white',
        ]"
      >
        Eventitude
      </h2>
    </div>

    <!-- Buttons -->
    <div class="flex max-lg:ml-auto space-x-4">
      <!-- Conditional Rendering for Login/Register or My Account -->
      <template v-if="!isLoggedIn">
        <a href="#/login">
          <button
            class="px-4 py-2 text-sm flex items-center justify-center rounded-full font-bold text-gray-100 hover:text-gray-700 border-2 bg-transparent hover:bg-gray-50 transition-all ease-in-out duration-300 space-x-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
              class="w-5 h-5"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6A2.25 2.25 0 005.25 5.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15M15 12h7.5m0 0l-3-3m3 3l-3 3"
              />
            </svg>
            <span>Login</span>
          </button>
        </a>
        <a href="#/register">
          <button
            class="px-4 py-2 text-sm rounded-full flex items-center justify-center font-bold text-white border-2 border-[#111827] bg-[#111827] transition-all ease-in-out duration-300 hover:bg-transparent hover:text-[#111827] space-x-2"
          >
            <span>Sign Up</span>
          </button>
        </a>
      </template>
      <template v-else>
        <a href="#/account">
          <button
            class="px-4 py-2 text-sm rounded-full flex items-center justify-center font-bold text-white border-2 border-[#111827] bg-[#111827] transition-all ease-in-out duration-300 hover:bg-transparent hover:text-[#111827] space-x-2"
          >
            <span>My Account</span>
          </button>
        </a>
      </template>
    </div>
  </div>
</template>
