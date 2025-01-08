<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { useToast } from "vue-toastification";

// ===================
// State references
// ===================
const events = ref([]); // List of events
console.log("🚀 ~ events:", events)
const user = ref(null); 
const loading = ref(false); // Loading state
const error = ref(null); // Error message (if any)
const isModalOpen = ref(false); // Modal visibility
const isUpdating = ref(false); // Whether updating or creating
const selectedEventId = ref(null); // ID of the event to update
const toast = useToast();

// ===================
// Event form
// ===================
const eventForm = ref({
  name: "",
  description: "",
  location: "",
  start_date: "",
  close_registration: "",
  max_attendees: "",
});

// ===================
// Fetch current user
// ===================
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

// ===================
// Fetch events
// ===================
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

// ==========================
// Create or Update an event
// ==========================
const saveEvent = async () => {
  const token = localStorage.getItem("authToken");
  if (!token) {
    toast.error("Unauthorized: Please log in.");
    return;
  }

  try {
    loading.value = true;

    if (isUpdating.value) {
      // -----------------------
      // Update existing event
      // -----------------------
      await axios.put(
        `${import.meta.env.VITE_BACKEND_URL}/api/events/${selectedEventId.value}`,
        { ...eventForm.value },
        {
          headers: { Authorization: `Bearer ${token}` },
        }
      );
      toast.success("Event updated successfully!");
    } else {
      // -----------------------
      // Create new event
      // -----------------------
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

// ===================
// Delete an event
// ===================
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

// ========================================
// Open the modal pre-filled for updating
// ========================================
const openUpdateModal = (event) => {
  isUpdating.value = true;
  selectedEventId.value = event.event_id; // or `event.id` if your API uses "id"
  eventForm.value = { ...event };
  isModalOpen.value = true;
};

// ===================
// Reset form
// ===================
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

// =========================
// On component mount
// =========================
onMounted(() => {
  fetchUserInfo();
  fetchEvents();
});
</script>

<template>
  <div class="min-h-screen p-4">
    <!-- ================
         User Info
         ================ -->
    <div v-if="user" class="flex items-center gap-4 p-6 bg-gray-100 rounded-md">
      <div class="w-full flex items-center justify-between">
        <div class="flex flex-row items-center gap-4">
          <!-- Avatar (initial) -->
          <div class="bg-[#FA7D3B] h-24 w-24 flex items-center justify-center rounded-full text-white text-3xl font-bold uppercase">
            {{ user.first_name ? user.first_name[0] : 'U' }}
          </div>

          <!-- Name & Email -->
          <div class="flex flex-col">
            <span class="text-gray-800 text-2xl font-bold capitalize">
              {{ user.first_name }} {{ user.last_name }}
            </span>
            <span class="text-gray-600">{{ user.email }}</span>
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

    <!-- ================
         Loading
         ================ -->
    <div v-if="loading" class="text-center text-gray-500 mt-6">
      Loading...
    </div>

    <!-- ================
         Events List
         ================ -->
    <div v-else class="mt-6">
      <!-- No events message -->
      <div v-if="events.length === 0" class="text-center text-gray-500">
        No events available.
      </div>

      <!-- Events Grid -->
      <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div
          v-for="event in events"
          :key="event.event_id" 
          class="border p-4 rounded-md shadow-md bg-white"
        >
          <h3 class="text-lg font-bold mb-2">{{ event.name }}</h3>
          <p class="text-sm text-gray-700 mb-1">
            {{ event.description }}
          </p>
          <p class="text-sm">
            <strong>Location:</strong> {{ event.location }}
          </p>
          <p class="text-sm">
            <strong>Start Date:</strong> {{ event.start_date }}
          </p>
          <p class="text-sm">
            <strong>Close Registration:</strong> {{ event.close_registration }}
          </p>
          <p class="text-sm">
            <strong>Max Attendees:</strong> {{ event.max_attendees }}
          </p>

          <!-- Action Buttons -->
          <div class="flex space-x-2 mt-4">
            <button
              @click="openUpdateModal(event)"
              class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition"
            >
              Update
            </button>
            <button
              @click="deleteEvent(event.event_id)"
              class="px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 transition"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- ================
         Modal
         ================ -->
    <div
      v-if="isModalOpen"
      class="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50 p-4"
    >
      <div class="bg-white rounded-lg shadow-lg w-full max-w-[500px] max-h-full overflow-y-auto p-6">
        <h3 class="text-xl font-bold mb-4">
          {{ isUpdating ? "Update Event" : "Create Event" }}
        </h3>

        <!-- Event Form -->
        <div class="space-y-4">
          <!-- Name -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Name
            </label>
            <input
              v-model="eventForm.name"
              type="text"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            />
          </div>
          <!-- Description -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Description
            </label>
            <textarea
              v-model="eventForm.description"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            ></textarea>
          </div>
          <!-- Location -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Location
            </label>
            <input
              v-model="eventForm.location"
              type="text"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            />
          </div>
          <!-- Start Date -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Start Date
            </label>
            <input
              v-model="eventForm.start_date"
              type="date"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            />
          </div>
          <!-- Close Registration -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Close Registration
            </label>
            <input
              v-model="eventForm.close_registration"
              type="date"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            />
          </div>
          <!-- Max Attendees -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Max Attendees
            </label>
            <input
              v-model="eventForm.max_attendees"
              type="number"
              class="mt-1 block w-full border p-2 rounded-md shadow-sm"
            />
          </div>
        </div>

        <!-- Modal Buttons -->
        <div class="mt-6 flex justify-end space-x-2">
          <button
            @click="() => { isModalOpen = false; resetForm(); }"
            class="px-4 py-2 bg-gray-300 rounded-md hover:bg-gray-400 transition"
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
