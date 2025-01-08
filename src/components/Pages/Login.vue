<script setup>
import { ref } from "vue";
import axios from "axios";
import { useToast } from "vue-toastification";

const email = ref("");
const password = ref("");
const showPassword = ref(false);
const loading = ref(false);
const toast = useToast();

const validateForm = () => {
  if (!email.value.trim() || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) {
    return "Valid email is required.";
  }
  if (!password.value.trim()) {
    return "Password is required.";
  }
  return null;
};

const login = async () => {
  const error = validateForm();
  if (error) {
    toast.error(error);
    return;
  }

  loading.value = true;

  try {
    const response = await axios.post(
      `${import.meta.env.VITE_BACKEND_URL}/api/user/login`,
      {
        email: email.value,
        password: password.value,
      }
    );

    toast.success("Login successful!");
    const { token, user } = response.data;

    localStorage.setItem("authToken", token);

    window.location.href = "#/";
  } catch (err) {
    const errorMessage =
      err.response?.data?.message || "Failed to log in. Please try again.";
    toast.error(errorMessage);
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <div class="font-[sans-serif] md:h-screen bg-black">
    <div class="grid md:grid-cols-2 items-center gap-8 h-full">
      <!-- Background Section -->
      <div class="relative max-md:order-1 h-full w-full">
        <img
          src="../../assets/event.jpeg"
          class="absolute top-0 left-0 w-full h-full object-cover"
          alt="Register Background"
        />
        <div class="absolute top-0 left-0 w-full h-full bg-black/50"></div>
        <div
          class="relative z-10 flex flex-col items-center justify-center h-full text-center px-4"
        >
          <h1 class="text-white text-4xl font-bold mb-4">
            Welcome Back to Eventitude
          </h1>
          <p class="text-white text-lg max-w-lg">
            Login to your account to manage events, connect with others, and
            explore amazing opportunities. Let’s make your next event
            unforgettable!
          </p>
        </div>
      </div>

      <!-- Login Form -->
      <div
        class="flex items-center md:p-8 p-6 bg-black shadow-sm md:rounded-tl-[55px] md:rounded-bl-[55px] h-full"
      >
        <form @submit.prevent="login" class="max-w-lg w-full mx-auto">
          <div class="mb-12">
            <h3 class="text-gray-100 text-4xl font-bold">Sign in</h3>
            <p class="text-gray-300 text-sm mt-4">
              Don't have an account
              <a
                href="#/register"
                class="text-blue-600 font-semibold hover:underline ml-1 whitespace-nowrap"
                >Register here</a
              >
            </p>
          </div>

          <!-- Email -->
          <div>
            <label class="text-gray-200 text-xs block mb-2">Email</label>
            <div class="relative flex items-center">
              <input
                v-model="email"
                type="email"
                required
                                            class="w-full text-sm border-b border-gray-100 bg-black text-white focus:border-gray-800 pl-2 py-3 outline-none"

                placeholder="Enter email"
              />
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="#bbb"
                stroke="#bbb"
                class="w-[18px] h-[18px] absolute right-2"
                viewBox="0 0 682.667 682.667"
              >
                <path d="M0 512h512V0H0Z" />
              </svg>
            </div>
          </div>

        
          <div class="mt-8">
            <label class="text-gray-200 text-xs block mb-2">Password</label>
            <div class="relative flex items-center">
              <input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                required
                                           class="w-full text-sm border-b border-gray-100 bg-black text-white focus:border-gray-800 pl-2 py-3 outline-none"

                placeholder="Enter password"
              />
            <svg
      @click="showPassword = !showPassword"
      xmlns="http://www.w3.org/2000/svg"
      :fill="showPassword ? '#F4683D' : '#bbb'"
      :stroke="showPassword ? '#F4683D' : '#bbb'"
      class="w-[18px] h-[18px] absolute right-2 cursor-pointer"
      viewBox="0 0 128 128"
              >
                <path
                  d="M64 104C22.127 104 1.367 67.496.504 65.943a4 4 0 0 1 0-3.887C1.367 60.504 22.127 24 64 24s62.633 36.504 63.496 38.057a4 4 0 0 1 0 3.887C126.633 67.496 105.873 104 64 104zM8.707 63.994C13.465 71.205 32.146 96 64 96c31.955 0 50.553-24.775 55.293-31.994C114.535 56.795 95.854 32 64 32 32.045 32 13.447 56.775 8.707 63.994zM64 88c-13.234 0-24-10.766-24-24s10.766-24 24-24 24 10.766 24 24-10.766 24-24 24zm0-40c-8.822 0-16 7.178-16 16s7.178 16 16 16 16-7.178 16-16-7.178-16-16-16z"
                />
              </svg>
            </div>
          </div>

          <div class="mt-12">
            <button
              :disabled="loading"
              type="submit"
              class="w-full py-3 px-6 text-sm font-semibold tracking-wider rounded-full text-white bg-[#F4683D] hover:bg-[#222] focus:outline-none"
            >
              {{ loading ? "Signing in..." : "Sign in" }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
