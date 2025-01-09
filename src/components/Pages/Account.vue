<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { useToast } from "vue-toastification";

const events = ref([]); 
console.log("🚀 ~ events:", events)
const user = ref(null); 
const loading = ref(false); 
const error = ref(null);
const isModalOpen = ref(false); 
const isUpdating = ref(false);
const selectedEventId = ref(null); 
const toast = useToast();


const eventForm = ref({
  name: "",
  description: "",
  location: "",
  start_date: "",
  close_registration: "",
  max_attendees: "",
});


const fetchUserInfo = async () => {
  const token = localStorage.getItem("authToken");
  if (!token) {
    toast.error("Unauthorized: No token found. Please log in.");
    return;
  }

  try {
    const response = await axios.get(
      `${import.meta.env.VITE_BACKEND_URL}/api/user/me`,
      {
        headers: { Authorization: `Bearer ${token}` },
      }
    );
    user.value = response.data.user;
  } catch (err) {
    toast.error(err.response?.data?.message || "Failed to fetch user information.");
  }
};

const fetchEvents = async () => {
  loading.value = true;
  const token = localStorage.getItem("authToken");

  try {
    const response = await axios.get(
      `${import.meta.env.VITE_BACKEND_URL}/api/events`,
      {
        headers: { Authorization: `Bearer ${token}` },
      }
    );
    console.log( response.data)
    events.value = response.data.events || [];
  } catch (err) {
    toast.error(err.response?.data?.message || "Failed to fetch events.");
  } finally {
    loading.value = false;
  }
};


const saveEvent = async () => {
  const token = localStorage.getItem("authToken");
  if (!token) {
    toast.error("Unauthorized: Please log in.");
    return;
  }

  try {
    loading.value = true;

    if (isUpdating.value) {

      await axios.put(
        `${import.meta.env.VITE_BACKEND_URL}/api/events/${selectedEventId.value}`,
        { ...eventForm.value },
        {
          headers: { Authorization: `Bearer ${token}` },
        }
      );
      toast.success("Event updated successfully!");
    } else {

      await axios.post(
        `${import.meta.env.VITE_BACKEND_URL}/api/events`,
        { ...eventForm.value },
        {
          headers: { Authorization: `Bearer ${token}` },
        }
      );
      toast.success("Event created successfully!");
    }

    resetForm();
    isModalOpen.value = false;
    fetchEvents();
  } catch (err) {
    toast.error(err.response?.data?.message || "Failed to save event.");
  } finally {
    loading.value = false;
  }
};

const deleteEvent = async (id) => {
  const token = localStorage.getItem("authToken");
  if (!token) {
    toast.error("Unauthorized: Please log in.");
    return;
  }

  try {
    loading.value = true;
    await axios.delete(
      `${import.meta.env.VITE_BACKEND_URL}/api/events/${id}`,
      {
        headers: { Authorization: `Bearer ${token}` },
      }
    );
    toast.success("Event deleted successfully!");
    fetchEvents();
  } catch (err) {
    toast.error(err.response?.data?.message || "Failed to delete event.");
  } finally {
    loading.value = false;
  }
};

const openUpdateModal = (event) => {
  isUpdating.value = true;
  selectedEventId.value = event.event_id; 
  eventForm.value = { ...event };
  isModalOpen.value = true;
};

const resetForm = () => {
  eventForm.value = {
    name: "",
    description: "",
    location: "",
    start_date: "",
    close_registration: "",
    max_attendees: "",
  };
  isUpdating.value = false;
  selectedEventId.value = null;
};

function formatDate(date) {
   const options = { day: 'numeric', month: 'long', year: 'numeric' };
   return new Date(date).toLocaleDateString('en-US', options); // Adjust locale as needed
 }

onMounted(() => {
  fetchUserInfo();
  fetchEvents();
});
</script>

<template>
  <div class="min-h-screen p-4 bg-black">
  
    <div v-if="user" class="flex items-center gap-4 p-6 bg-neutral-800 rounded-md">
      <div class="w-full flex flex-col sm:flex-row gap-4 sm:items-center justify-between">
        <div class="flex flex-row items-center gap-4">
          <div class="bg-[#FA7D3B] h-24 w-24 flex items-center justify-center rounded-full text-white text-3xl font-bold uppercase">
            {{ user.first_name ? user.first_name[0] : 'U' }}
          </div>

          <div class="flex flex-col">
            <span class="text-gray-100 text-xl font-semibold sm:text-2xl sm:font-bold capitalize">
              {{ user.first_name }} {{ user.last_name }}
            </span>
            <span class="text-gray-300">{{ user.email }}</span>
          </div>
        </div>

        <!-- Create Event Button -->
        <button
          @click="isModalOpen = true"
          class="px-6 py-2 bg-[#FA7D3B] text-white rounded-full hover:bg-[#e77026] transition"
        >
          Create Event
        </button>
      </div>
    </div>

    <div v-if="loading" class="text-center text-gray-500 mt-6">
      Loading...
    </div>


    <div v-else class="mt-6">
      <div v-if="events.length === 0" class="text-center text-gray-500">
        No events available.
      </div>

      <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 2xl:grid-cols-2 gap-4">
  <div
    v-for="event in events"
    :key="event.event_id"
    class="p-4 rounded-xl shadow-md  bg-neutral-900 border border-neutral-700"
  >
   <div class="flex space-x-4 mb-4 items-center justify-end">
      <!-- Edit Button -->
      <button
        @click="openUpdateModal(event)"
        class="flex items-center justify-center w-10 h-10 bg-blue-600 text-white rounded-full hover:bg-blue-700 transition"
        aria-label="Edit"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          class="w-6 h-6"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M15.232 5.232l3.536 3.536M4 20h4.75a2 2 0 001.414-.586l10.243-10.243a2 2 0 000-2.828l-3.536-3.536a2 2 0 00-2.828 0L4.586 15.414A2 2 0 004 16.828V20z"
          />
        </svg>
      </button>

      <!-- Delete Button -->
      <button
        @click="deleteEvent(event.event_id)"
        class="flex items-center justify-center w-10 h-10 bg-red-600 text-white rounded-full hover:bg-red-700 transition"
        aria-label="Delete"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          class="w-6 h-6"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5-4h4m-4 0a1 1 0 00-1 1v1h6V4a1 1 0 00-1-1m-4 0h4"
          />
        </svg>
      </button>
    </div>
    <h3 class="text-2xl text-white capitalize lg:text-3xl xl:text-4xl font-bold mb-2">
      {{ event.name }}
    </h3>
    <p class="text-sm text-gray-400 mb-6">{{ event.description }}</p>
    <p class="text-sm text-gray-300">
      <strong>Location:</strong> {{ event.location }}
    </p>
    <p class="text-sm text-gray-300 mt-1">
      <strong>Max Attendees:</strong> {{ event.max_attendees }}
    </p>
    <div class="flex flex-col md:flex-row md:items-center justify-between mt-1 space-y-2 md:space-y-0">
  <p class="text-sm text-gray-300">
    <strong>Start Date:</strong> {{ formatDate(event.start_date) }}
  </p>
  <p class="text-sm text-gray-300">
    <strong>Close Registration:</strong> {{ formatDate(event.close_registration) }}
  </p>


</div>
<div class="flex items-center justify-end mt-9">
<a
  :href="`#/events/${event.event_id}`"
  class="px-6 py-2 bg-[#FA7D3B] text-white rounded-full hover:bg-[#e77026] transition inline-block text-center "
>
  Detail
</a>


</div>


   
  </div>
</div>

    </div>

    <div
      v-if="isModalOpen"
      class="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50 p-4"
    >
      <div class="bg-neutral-800 rounded-lg shadow-lg w-full max-w-[500px] max-h-full overflow-y-auto p-6">
        <h3 class="text-xl font-bold text-white mb-4">
          {{ isUpdating ? "Update Event" : "Create Event" }}
        </h3>

        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-300">
              Name
            </label>
            <input
              v-model="eventForm.name"
              type="text"
              placeholder="Enter Event Name"
              class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-300">
              Description
            </label>
            <textarea
              v-model="eventForm.description"
              placeholder="Enter Event Detail"
                           class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"
            ></textarea>
          </div>
        
                  <div class="grid grid-cols-1 lg:grid-cols-2 gap-3">

          <div>
            <label class="block text-sm font-medium text-gray-300">
              Start Date
            </label>
            <input
              v-model="eventForm.start_date"
              type="date"
                                       class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"

            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-300">
              Close Registration
            </label>
            <input
              v-model="eventForm.close_registration"
              type="date"
                                      class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"

            />
          </div>
          </div>
          <div class="grid grid-cols-1 lg:grid-cols-2 gap-3">
              <div>
            <label class="block text-sm font-medium text-gray-300">
              Location
            </label>
            <input
              v-model="eventForm.location"
              placeholder="Enter Event Location"
              type="text"
                                       class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"

            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-300">
              Max Attendees
            </label>
            <input
              v-model="eventForm.max_attendees"
              placeholder="Enter Max Attendees Event"
              type="number"
                                      class=" block w-full mt-2 bg-neutral-700 outline-none text-white p-2 rounded-md shadow-sm"

            />
          </div>
        </div>
        </div>

        <div class="mt-6 flex justify-end space-x-2">
          <button
            @click="() => { isModalOpen = false; resetForm(); }"
            class="px-4 py-2 bg-gray-700 text-white rounded-md hover:bg-gray-800 transition"
          >
            Cancel
          </button>
          <button
            @click="saveEvent"
            class="px-4 py-2 bg-[#FA7D3B] text-white rounded-md hover:bg-[#e77026] transition"
          >
            {{ isUpdating ? "Update" : "Create" }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
