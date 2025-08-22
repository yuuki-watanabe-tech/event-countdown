<script setup lang="ts">
import { computed, defineProps, onMounted, onUnmounted, ref } from "vue";
import type { Countdown } from "../types/countdown";

const props = defineProps<Countdown>();

const days = ref(0)
const hours = ref(0)
const minutes = ref(0)
const seconds = ref(0)
const isFuture = ref(false)
let timerId: number | undefined = undefined

defineEmits<{
  delete: [id: number]
}>();

// イベント種類ごとの背景色
const eventBackground = computed(() => {
  switch (props.eventType) {
    case "birthday":
      return { 'background-image': "url('/birthday.png')" }
    case "work":
      return { 'background-image': "url('/work.png')" }
    case "trip":
      return { 'background-image': "url('/trip.png')" }
    case "anniversary":
      return { 'background-image': "url('/anniversary.png')" }
    default:
      return { 'background-image': "url('/default.png')" }
  }
})

const eventFontColor = computed(() => {
  switch (props.eventType) {
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

const startCountdown = (timerId: number | undefined) => {
  clearInterval(timerId)
  const target = new Date(props.targetDate)

  const update = () => {
    const now = new Date()
    const diff = target.getTime() - now.getTime()

    if (diff <= 0) {
      isFuture.value = false;
      clearInterval(timerId)
      return
    }

    days.value = Math.floor(diff / (1000 * 60 * 60 * 24))
    hours.value = Math.floor((diff / (1000 * 60 * 60)) % 24)
    minutes.value = Math.floor((diff / (1000 * 60)) % 60)
    seconds.value = Math.floor((diff / 1000) % 60)

    isFuture.value = true
  }

  update()
  timerId = setInterval(update, 1000)
}

onMounted(() => startCountdown(timerId))
onUnmounted(() => clearInterval(timerId))

</script>

<template>
  <!-- Timer Result -->
  <div class="shadow-[0_10px_25px_rgba(0,0,0,0.25)] mt-8 p-2 rounded-xl text-white relative" :style="eventBackground">
    <!-- ✕ ボタン -->
    <button @click="$emit('delete', props.id)" class="absolute top-2 right-2 w-8 h-8 flex items-center justify-center
      bg-white text-gray-600 font-bold rounded-full
      shadow-md hover:shadow-lg hover:text-gray-800
      active:shadow-inner transition"> ✕
    </button>

    <p v-if="isFuture" class="text-lg mb-4" :class="eventFontColor">
      {{ props.eventName || "Event" }} starts in:
    </p>
    <div v-if="isFuture" class="flex justify-center space-x-4">
      <div class="text-center">
        <p class="text-5xl font-extrabold drop-shadow-lg" :class="eventFontColor">{{ days }}</p>
        <span class="text-sm uppercase" :class="eventFontColor">Days</span>
      </div>
      <div class="text-center">
        <p class="text-5xl font-extrabold drop-shadow-lg" :class="eventFontColor">{{ hours }}</p>
        <span class="text-sm uppercase" :class="eventFontColor">Hours</span>
      </div>
      <div class="text-center">
        <p class="text-5xl font-extrabold drop-shadow-lg" :class="eventFontColor">{{ minutes }}</p>
        <span class="text-sm uppercase" :class="eventFontColor">Minutes</span>
      </div>
      <div class="text-center">
        <p class="text-5xl font-extrabold drop-shadow-lg" :class="eventFontColor">{{ seconds }}</p>
        <span class="text-sm uppercase" :class="eventFontColor">Seconds</span>
      </div>
    </div>
    <p v-else class="text-2xl font-bold mt-4">
      {{ eventName || "Event" }} has passed!
    </p>
  </div>
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