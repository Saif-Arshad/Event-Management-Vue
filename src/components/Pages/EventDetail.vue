<template>
  <div class="event-detail bg-neutral-800 min-h-screen flex flex-col items-center justify-center px-4">
    <!-- Loading State -->
    <div v-if="loading" class="text-center text-gray-500 text-lg">
      Loading...
    </div>

    <!-- Event Details -->
    <div v-else class="grid md:grid-cols-3 px-6 h-full md:max-h-screen md:overflow-y-hidden">
      <!-- Event Info -->
      <div
        class="w-full md:row-span-2 md:col-span-2 bg-neutral-800 rounded-lg p-6 sm:p-10"
      >
        <h1 class="text-3xl md:text-5xl text-white font-bold mb-4 capitalize">
          {{ event.name }}
        </h1>
        <p class="text-gray-300 mb-6">
          {{ event.description }}
        </p>
        <div class="text-gray-200 space-y-3">
          <p>
            <strong class="text-gray-100">Location:</strong> {{ event.location }}
          </p>
          <p>
            <strong class="text-gray-100">Max Attendees:</strong>
            {{ event.max_attendees }}
          </p>
          <div class="grid md:grid-cols-2 gap-3">
            <p>
              <strong class="text-gray-100">Start Date:</strong>
              {{ formatDate(event.start_date) }}
            </p>
            <p>
              <strong class="text-gray-100">Close Registration:</strong>
              {{ formatDate(event.close_registration) }}
            </p>
          </div>
          <div class="flex items-center justify-end mt-10">
            <button
              class="px-6 py-2 bg-[#FA7D3B] mt-6 text-white rounded-full hover:bg-[#e77026] transition inline-block text-center"
            >
              Join Event
            </button>
          </div>
        </div>
      </div>

      <!-- Questions Section -->
      <div class="w-full bg-neutral-800 md:border-l h-screen relative p-6">
        <h1 class="text-3xl text-white font-bold mb-4 capitalize">
          Ask Questions
        </h1>

        <!-- Questions List -->
        <div class="mb-4 space-y-3">
          <div
            v-for="question in questions"
            :key="question.question_id"
            class="bg-neutral-900 p-4 capitalize rounded-lg text-white"
          >
            {{ question.question }}
          </div>
        </div>

        <!-- Ask Question Form -->
        <div class="absolute bottom-5 left-6 right-6">
          <div class="flex gap-5">
            <input
              v-model="newQuestion"
              type="text"
              class="bg-neutral-900 p-3 w-full rounded-lg text-white placeholder-gray-400 
                     focus:outline-none focus:ring-2 focus:ring-[#FA7D3B] focus:ring-opacity-50"
              placeholder="Write Question"
            />

            <button
              @click="askQuestion"
              class="px-6 py-2 bg-[#FA7D3B] text-white rounded-full hover:bg-[#e77026] 
                     transition-colors inline-block text-center font-medium"
            >
              Ask
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { useToast } from "vue-toastification";

const eventId = ref(window.location.hash.split("/").pop());
const loading = ref(false);
const event = ref({});
const questions = ref([]);
const newQuestion = ref(""); // For the input field
const toast = useToast();

function formatDate(date) {
  const options = { day: "numeric", month: "long", year: "numeric" };
  return new Date(date).toLocaleDateString("en-US", options); 
}

// Fetch Event Details
const fetchEvent = async () => {
  loading.value = true;
  try {
    const response = await axios.get(
      `${import.meta.env.VITE_BACKEND_URL}/api/events/${eventId.value}`
    );
    event.value = response.data.event || {};
  } catch (err) {
    console.error("Error fetching event:", err);
  } finally {
    loading.value = false;
  }
};

const fetchQuestions = async () => {
  try {
    const response = await axios.get(
      `${import.meta.env.VITE_BACKEND_URL}/api/events/questions`,
      { params: { event_id: eventId.value } }
    );
    questions.value = response.data.questions || [];
  } catch (err) {
    console.error("Error fetching questions:", err);
  }
};

const askQuestion = async () => {
  if (!newQuestion.value.trim()) {
    toast.error("Question cannot be empty!");
    return;
  }

  try {
    const token = localStorage.getItem("authToken"); 
  if(!token){
    toast.error("Login First to ask any Question")
  }
    const response = await axios.post(
      `${import.meta.env.VITE_BACKEND_URL}/api/events/question`,
      {
        question: newQuestion.value,
        question_id: Date.now(),
        event_id: eventId.value,
      },
      {
        headers: {
          Authorization: `Bearer ${token}`, 
        },
      }
    );

    // Add the new question to the list
    questions.value.push(response.data.data);
    newQuestion.value = ""; // Clear the input field
  } catch (err) {
    console.error("Error asking question:", err);
    toast.err("Failed to submit the question. Please try again.");
  }
};

onMounted(() => {
  fetchEvent();
  fetchQuestions();
});
</script>


