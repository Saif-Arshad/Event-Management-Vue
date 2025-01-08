<script setup>
import { ref } from "vue";
import axios from "axios";
import { useToast } from "vue-toastification";

const firstName = ref("");
const lastName = ref("");
const email = ref("");
const password = ref("");
const showPassword = ref(false);
const loading = ref(false);
const toast = useToast();

const validateForm = () => {
  if (!firstName.value.trim()) return "First name is required.";
  if (!lastName.value.trim()) return "Last name is required.";
  if (!email.value.trim() || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value))
    return "Valid email is required.";
  if (!password.value.trim() || password.value.length < 6)
    return "Password must be at least 6 characters.";
  return null;
};

const register = async () => {
  const error = validateForm();
  if (error) {
    toast.error(error);
    return;
  }

  loading.value = true;

  try {
    const response = await axios.post(
      `${import.meta.env.VITE_BACKEND_URL}/api/user/register`,
      {
        first_name: firstName.value,
        last_name: lastName.value,
        email: email.value,
        password: password.value,
      }
    );
    toast.success("Registration successful!");
    firstName.value = "";
    lastName.value = "";
    email.value = "";
    password.value = "";
     window.location.href = "#/login";
  } catch (err) {
    const errorMessage =
      err.response?.data?.message || "Failed to register. Please try again.";
    toast.error(errorMessage);
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <div class="font-[sans-serif] md:h-screen">
    <div class="grid md:grid-cols-2 items-center gap-8 h-full">
      <div
        class="flex items-center md:p-8 p-6 bg-white md:rounded-tr-[55px] md:rounded-br-[55px] h-full"
      >
        <form @submit.prevent="register" class="max-w-lg w-full mx-auto">
          <div class="mb-12">
            <h3 class="text-gray-800 text-2xl sm:text-4xl font-bold">
              Create Your Account
            </h3>
            <p class="text-gray-800 text-sm mt-4">
              Already have an account
              <a
                href="#/login"
                class="text-blue-600 font-semibold hover:underline ml-1 whitespace-nowrap"
                >Login here</a
              >
            </p>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 mb-5 gap-x-5">
            <div>
              <label class="text-gray-800 text-xs block mb-2">First Name</label>
              <input
                v-model="firstName"
                name="first_name"
                type="text"
                class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 py-3 outline-none"
                placeholder="Enter your first name"
                required
              />
            </div>
            <div>
              <label class="text-gray-800 text-xs block mb-2">Last Name</label>
              <input
                v-model="lastName"
                name="last_name"
                type="text"
                class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 py-3 outline-none"
                placeholder="Enter your last name"
                required
              />
            </div>
          </div>

          <div class="mb-5">
            <label class="text-gray-800 text-xs block mb-2">Email</label>
            <input
              v-model="email"
              name="email"
              type="email"
              class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 py-3 outline-none"
              placeholder="Enter email"
              required
            />
          </div>

          <div>
            <label class="text-gray-800 text-xs block mb-2">Password</label>
            <div class="relative">
              <input
                v-model="password"
                name="password"
                :type="showPassword ? 'text' : 'password'"
                class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 pr-10 py-3 outline-none"
                placeholder="Enter password"
                required
              />
              <svg
                @click="showPassword = !showPassword"
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5 absolute right-2 top-2.5 cursor-pointer"
                fill="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  v-if="!showPassword"
                  d="M12 5c-7 0-12 7-12 7s5 7 12 7 12-7 12-7-5-7-12-7zm0 12c-2.762 0-5-2.239-5-5s2.238-5 5-5 5 2.239 5 5-2.238 5-5 5zm-1-3h2v2h-2zm-2-3h6v2h-6zm1-1h4v-2h-4z"
                />
                <path
                  v-else
                  d="M1 12s5-7 11-7 11 7 11 7-5 7-11 7-11-7-11-7zm11-5c-4.253 0-8.478 3.134-10 5 1.522 1.866 5.747 5 10 5s8.478-3.134 10-5c-1.522-1.866-5.747-5-10-5zm0 6a2 2 0 1 1-.001-3.999A2 2 0 0 1 12 13z"
                />
              </svg>
            </div>
          </div>

          <div class="mt-12">
            <button
              :disabled="loading"
              class="w-full py-3 px-6 text-sm font-semibold tracking-wider rounded-full text-white bg-[#F4683D] hover:bg-[#222] focus:outline-none"
            >
              {{ loading ? "Registering..." : "Create Account" }}
            </button>
          </div>
        </form>
      </div>

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
            Join the World of Events
          </h1>
          <p class="text-white text-lg max-w-lg">
            Create, join, and explore exciting events on Eventitude. Meet
            like-minded people, share your experiences, and make every moment
            unforgettable!
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
