<script setup>
import { computed, onUnmounted, ref } from "vue"

const eventName = ref("")
const targetDate = ref("")
const eventType = ref("birthday") // 初期値
const result = ref(null)
const errorMessage = ref("")
let timer = null

// イベント種類ごとの背景色
const eventBackground = computed(() => {
  switch (eventType.value) {
    case "birthday":
      return "bg-gradient-to-br from-pink-500 to-green-400"
    case "work":
      return "bg-gradient-to-br from-gray-600 to-yellow-400"
    case "trip":
      return "bg-gradient-to-br from-blue-500 to-orange-400"
    case "anniversary":
      return "bg-gradient-to-br from-red-500 to-cyan-400"
    default:
      return "bg-gradient-to-br from-orange-500 to-indigo-500"
  }
})

const startCountdown = () => {
  errorMessage.value = ""

  if (!eventName.value.trim()) {
    errorMessage.value = "Please enter an Event Name."
    return
  }
  if (!targetDate.value) {
    errorMessage.value = "Please select a Target Date & Time."
    return
  }

  clearInterval(timer)
  const target = new Date(targetDate.value)

  const update = () => {
    const now = new Date()
    const diff = target - now

    if (diff <= 0) {
      result.value = { isFuture: false }
      clearInterval(timer)
      return
    }

    const days = Math.floor(diff / (1000 * 60 * 60 * 24))
    const hours = Math.floor((diff / (1000 * 60 * 60)) % 24)
    const minutes = Math.floor((diff / (1000 * 60)) % 60)
    const seconds = Math.floor((diff / 1000) % 60)

    result.value = {
      isFuture: true,
      days,
      hours,
      minutes,
      seconds
    }
  }

  update()
  timer = setInterval(update, 1000)
}

const deleteCountdown = () => {
  result.value = null;
  clearInterval(timer)
}

onUnmounted(() => clearInterval(timer))
</script>

<template>
  <div
    class="min-h-screen bg-gradient-to-br from-slate-900 to-slate-700  flex items-center justify-center text-gray-900 px-4 py-8">
    <div class="bg-white shadow-lg rounded-2xl p-8 max-w-lg w-full text-center">
      <!-- Title -->

      <h1 class="text-5xl font-extrabold dark:text-black mb-4">Event Countdown</h1>


      <!-- Event Name -->
      <div class="mb-4 text-left">
        <label class="block text-sm font-medium mb-1">Event Name</label>
        <input type="text" v-model="eventName" placeholder="e.g. My Trip"
          class="border rounded-lg p-2 w-full focus:ring-2 focus:ring-orange-400 focus:outline-none" />
      </div>

      <!-- Event Type -->
      <div class="mb-4 text-left">
        <label class="block text-sm font-medium mb-1">Event Type</label>
        <select v-model="eventType"
          class="border rounded-lg p-2 w-full focus:ring-2 focus:ring-orange-400 focus:outline-none">
          <option value="notChoosing">not choosing</option>
          <option value="birthday">🎉 Birthday</option>
          <option value="work">💼 Work / Deadline</option>
          <option value="trip">🌍 Trip / Travel</option>
          <option value="anniversary">❤️ Anniversary</option>
        </select>
      </div>

      <!-- Target Date -->
      <div class="mb-4 text-left">
        <label class="block text-sm font-medium mb-1">Target Date & Time</label>
        <input type="datetime-local" v-model="targetDate"
          class="border rounded-lg p-2 w-full focus:ring-2 focus:ring-orange-400 focus:outline-none" />
      </div>

      <!-- Button -->
      <button @click="startCountdown"
        class="bg-blue-500 hover:bg-blue-600 text-white px-6 py-2 rounded-lg w-full font-semibold">
        Start Countdown
      </button>

      <!-- Error Message -->
      <p v-if="errorMessage" class="text-red-500 text-sm mt-2">
        {{ errorMessage }}
      </p>

      <!-- Timer Result -->
      <div v-if="result" class="mt-8 p-2 rounded-xl text-white relative" :class="eventBackground">
        <!-- ✕ ボタン -->
        <button @click="deleteCountdown" class="absolute top-2 right-2 w-8 h-8 flex items-center justify-center
         bg-white text-gray-600 font-bold rounded-full
         shadow-md hover:shadow-lg hover:text-gray-800
         active:shadow-inner transition"> ✕
        </button>

        <p v-if="result.isFuture" class="text-lg mb-4">
          {{ eventName || "Event" }} starts in:
        </p>
        <div v-if="result.isFuture" class="flex justify-center space-x-4">
          <div class="text-center">
            <p class="text-5xl font-extrabold">{{ result.days }}</p>
            <span class="text-sm uppercase">Days</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold">{{ result.hours }}</p>
            <span class="text-sm uppercase">Hours</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold">{{ result.minutes }}</p>
            <span class="text-sm uppercase">Minutes</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold">{{ result.seconds }}</p>
            <span class="text-sm uppercase">Seconds</span>
          </div>
        </div>
        <p v-else class="text-2xl font-bold mt-4">
          {{ eventName || "Event" }} has passed!
        </p>
      </div>
    </div>
  </div>

  <footer class="mt-12 py-6 border-t text-center text-sm text-gray-500">
    <a href="/about.html" class="mx-3 hover:text-orange-500">About</a>
    <a href="/privacy.html" class="mx-3 hover:text-orange-500">Privacy Policy</a>
  </footer>
</template>

<style scoped>
@keyframes gradient {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

.bg-animated {
  background: linear-gradient(-45deg, #ff9a9e, #fad0c4, #fbc2eb, #a18cd1);
  background-size: 400% 400%;
  animation: gradient 15s ease infinite;
}
</style>