<script setup lang="ts">
import { computed, onUnmounted, ref } from "vue"

interface Countdown {
  isFuture?: boolean
  days?: number
  hours?: number
  minutes?: number
  seconds?: number
}

const eventName = ref("")
const targetDate = ref("")
const eventType = ref("notChoosing")
const result = ref<Countdown>()
const errorMessage = ref("")
let timer: number | undefined = undefined

// イベント種類ごとの背景色
const eventBackground = computed(() => {
  switch (eventType.value) {
    case "birthday":
      return "bg-pink-200"
    case "work":
      return "bg-yellow-200"
    case "trip":
      return "bg-orange-200"
    case "anniversary":
      return "bg-cyan-200"
    default:
      return "bg-white-200"
  }
})

const eventFontColor = computed(() => {
  switch (eventType.value) {
    case "birthday":
      return "text-pink-500"
    case "work":
      return "text-yellow-500"
    case "trip":
      return "text-orange-500"
    case "anniversary":
      return "text-cyan-500"
    default:
      return "text-black"
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
    const diff = target.getTime() - now.getTime()

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
  result.value = {};
  clearInterval(timer)
}

function setPreset(days: number) {
  const now = new Date()
  now.setDate(now.getDate() + days)
  // datetime-local 用にフォーマット
  targetDate.value = now.toISOString().slice(0, 16)
}

onUnmounted(() => clearInterval(timer))
</script>

<template>
  <div
    class="min-h-screen bg-gradient-to-br from-slate-400 to-slate-100  flex items-center justify-center text-gray-900 px-4 py-8"
    style="background-image: url('/bg-img.png');">
    <div class="bg-white shadow-lg rounded-2xl p-8 max-w-lg w-full text-center">

      <!-- Title -->
      <h1 class="text-5xl font-extrabold dark:text-black mb-4">Event Countdown</h1>
      <p class="text-lg text-gray-400 mb-6 text-center max-w-2xl">
        Create and track countdowns for your important events.
        Whether it’s a birthday, a trip, or a special milestone,
        stay excited as the big day approaches.
      </p>

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
        <!-- プリセットボタン -->
        <div class="flex gap-2 mt-2 mb-2">
          <button @click="setPreset(1)" class="bg-gray-200 text-gray-800 px-3 py-1 rounded-md hover:bg-gray-300">
            +1 day
          </button>
          <button @click="setPreset(7)" class="bg-gray-200 text-gray-800 px-3 py-1 rounded-md hover:bg-gray-300">
            +1 week
          </button>
          <button @click="setPreset(14)" class="bg-gray-200 text-gray-800 px-3 py-1 rounded-md hover:bg-gray-300">
            +2 weeks
          </button>
        </div>
        <input type="datetime-local" v-model="targetDate"
          class="border rounded-lg p-2 w-full focus:ring-2 focus:ring-orange-400 focus:outline-none" />
      </div>

      <!-- Button -->
      <button type="button" @click="startCountdown"
        class="text-white bg-gradient-to-r from-blue-500 via-blue-600 to-blue-700 hover:bg-gradient-to-br dark:focus:ring-blue-800 shadow-lg shadow-blue-500/50 dark:shadow-lg dark:shadow-blue-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center me-2 mb-2 ">
        Start Countdown
      </button>

      <!-- Error Message -->
      <p v-if="errorMessage" class="text-red-500 text-sm mt-2">
        {{ errorMessage }}
      </p>

      <!-- Timer Result -->
      <div v-if="result" class="shadow-[0_10px_25px_rgba(0,0,0,0.25)] mt-8 p-2 rounded-xl text-white relative"
        :class="eventBackground">
        <!-- ✕ ボタン -->
        <button @click="deleteCountdown" class="absolute top-2 right-2 w-8 h-8 flex items-center justify-center
         bg-white text-gray-600 font-bold rounded-full
         shadow-md hover:shadow-lg hover:text-gray-800
         active:shadow-inner transition"> ✕
        </button>

        <p v-if="result.isFuture" class="text-lg mb-4" :class="eventFontColor">
          {{ eventName || "Event" }} starts in:
        </p>
        <div v-if="result.isFuture" class="flex justify-center space-x-4">
          <div class="text-center">
            <p class="text-5xl font-extrabold" :class="eventFontColor">{{ result.days }}</p>
            <span class="text-sm uppercase" :class="eventFontColor">Days</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold" :class="eventFontColor">{{ result.hours }}</p>
            <span class="text-sm uppercase" :class="eventFontColor">Hours</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold" :class="eventFontColor">{{ result.minutes }}</p>
            <span class="text-sm uppercase" :class="eventFontColor">Minutes</span>
          </div>
          <div class="text-center">
            <p class="text-5xl font-extrabold" :class="eventFontColor">{{ result.seconds }}</p>
            <span class="text-sm uppercase" :class="eventFontColor">Seconds</span>
          </div>
        </div>
        <p v-else class="text-2xl font-bold mt-4">
          {{ eventName || "Event" }} has passed!
        </p>
      </div>
    </div>
  </div>

  <footer class="py-6 border-t text-center text-sm text-gray-500">
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