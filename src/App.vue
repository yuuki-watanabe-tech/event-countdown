<script setup lang="ts">
import { onUnmounted, ref } from "vue"
import CountDownCard from "./components/CountDownCard.vue"
import type { Countdown } from "./types/countdown"

const eventName = ref("")
const targetDate = ref("")
const eventType = ref("notChoosing")
const countdownList = ref<Countdown[]>([])
const errorMessage = ref("")
let timer: number | undefined = undefined

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

  const diff = new Date(targetDate.value).getTime() - new Date().getTime()
  if (diff <= 0) {
    errorMessage.value = "Please enter the future date."
    return
  }

  countdownList.value.push({
    id: generateId(),
    eventName: eventName.value,
    eventType: eventType.value,
    targetDate: targetDate.value,
    isFuture: false
  });
}

const deleteCountdown = (id: number) => {
  console.log('deleteCountdown. id:' + id)
  countdownList.value = countdownList.value.filter(e => e.id !== id);
}

function setPreset(days: number) {
  const now = new Date()
  now.setDate(now.getDate() + days)
  // datetime-local 用にフォーマット
  targetDate.value = now.toISOString().slice(0, 16)
}

function generateId(): number {
  return Date.now() * 1_000_000 + Math.floor(Math.random() * 1_000_000)
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
      <CountDownCard v-for="countdown in countdownList" :id="countdown.id" :eventName="countdown.eventName"
        :eventType="countdown.eventType" :targetDate="countdown.targetDate" :isFuture="countdown.isFuture"
        @delete="deleteCountdown" />
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