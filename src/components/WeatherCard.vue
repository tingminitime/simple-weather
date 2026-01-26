<script setup lang="ts">
import type { AnimationPlaybackControls } from 'motion-v'
import { animate, motion, RowValue, useMotionValue, useTransform } from 'motion-v'

defineOptions({
  name: 'WeatherCard',
})

const {
  weatherData,
  temperature,
  feelsLike,
  humidity,
  windSpeed,
  visibility,
  weatherIconUrl,
  weatherDescription,
  cityName,
  fullLocation,
  isLoading,
  error,
  getCurrentLocation,
  refetch,
} = useWeather()

const count = useMotionValue(0)
const roundedTemperature = useTransform(() => Math.round(count.get()))

let controls: AnimationPlaybackControls

async function handleRefresh() {
  await refetch()
  controls.play()
}

onMounted(async () => {
  await getCurrentLocation()
  controls = animate(count, temperature.value, { duration: 1.5 })
})

onUnmounted(() => {
  controls?.stop()
})
</script>

<template>
  <div class="mx-auto h-144 max-w-md p-6">
    <div
      class="
        h-full rounded-2xl from-stone-100 to-primary-light p-8 text-stone-700
        shadow-2xl
        not-dark:bg-linear-to-br
        dark:bg-primary-dark dark:text-stone-100
      "
    >
      <!-- 標題 -->
      <div class="mb-6 flex items-center justify-between">
        <h2 class="text-2xl font-bold">
          當前天氣
        </h2>
        <button
          type="button"
          :disabled="isLoading"
          class="
            block rounded-full p-2 leading-0 transition-colors
            not-disabled:hover:bg-gray-200
            disabled:opacity-50
          "
          title="重新整理"
          @click="handleRefresh"
        >
          <span class="icon-[carbon--rotate-360] size-6"></span>
        </button>
      </div>

      <!-- 載入中狀態 -->
      <div
        v-if="isLoading"
        class="flex flex-col items-center justify-center py-12"
      >
        <div
          class="
            size-16 animate-spin rounded-full border-4 border-stone-400
            border-t-transparent
            dark:border-stone-300 dark:border-t-primary-dark
          "
        ></div>
        <p class="mt-4 text-stone-700/70 dark:text-stone-300">
          正在取得天氣資訊...
        </p>
      </div>

      <!-- 錯誤狀態 -->
      <div
        v-else-if="error"
        class="rounded-lg border border-red-300 bg-red-500/20 p-4"
      >
        <div class="flex items-start">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="mr-2 size-6 shrink-0"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
            />
          </svg>
          <div>
            <h3 class="mb-1 font-semibold">
              發生錯誤
            </h3>
            <p class="text-sm text-white/90">
              {{ error }}
            </p>
            <button
              class="
                mt-3 rounded-lg bg-white px-4 py-2 text-sm font-medium
                text-blue-600 transition-colors
                hover:bg-white/90
              "
              @click="getCurrentLocation"
            >
              重試
            </button>
          </div>
        </div>
      </div>

      <!-- 天氣資訊 -->
      <div
        v-else-if="weatherData"
        class="text-center"
      >
        <!-- 天氣圖示和溫度 -->
        <div class="mb-4 flex items-center justify-center gap-x-4">
          <!-- 天氣 Icon -->
          <motion.div
            class="size-16 rounded-full bg-stone-300"
            :initial="{ scale: 0 }"
            :animate="{ scale: 1 }"
          >
            <img
              v-if="weatherIconUrl"
              :src="weatherIconUrl"
              :alt="weatherDescription"
            >
          </motion.div>
          <!-- 溫度 -->
          <div class="mr-8 w-20 -translate-y-1 text-7xl font-bold">
            <motion.pre><RowValue :value="roundedTemperature" />&deg;</motion.pre>
          </div>
        </div>

        <!-- 天氣描述 -->
        <motion.p
          class="
            mb-6 text-xl font-semibold text-stone-500 capitalize
            dark:text-stone-300
          "
          :initial="{ opacity: 0 }"
          :animate="{ opacity: 1 }"
        >
          {{ weatherDescription }}
        </motion.p>

        <!-- 位置 -->
        <motion.p
          class="text-stone-500 dark:text-stone-300"
          :initial="{ opacity: 0 }"
          :animate="{ opacity: 1 }"
          :transition="{ delay: 0.2 }"
        >
          {{ fullLocation }}
        </motion.p>

        <!-- 詳細資訊 -->
        <div class="mt-6 grid grid-cols-2 gap-4 border-white/20">
          <motion.div
            class="rounded-lg bg-white/10 p-4"
            :initial="{ translateY: 20, opacity: 0 }"
            :animate="{ translateY: 0, opacity: 1 }"
            :transition="{ delay: 0.3, duration: 0.5 }"
          >
            <p class="mb-1 text-sm text-stone-700/70 dark:text-stone-300">
              體感溫度
            </p>
            <p class="text-2xl font-semibold">
              {{ feelsLike }}°
            </p>
          </motion.div>

          <motion.div
            class="rounded-lg bg-white/10 p-4"
            :initial="{ translateY: 20, opacity: 0 }"
            :animate="{ translateY: 0, opacity: 1 }"
            :transition="{ delay: 0.6, duration: 0.5 }"
          >
            <p class="mb-1 text-sm text-stone-700/70 dark:text-stone-300">
              濕度
            </p>
            <p class="text-2xl font-semibold">
              {{ humidity }}%
            </p>
          </motion.div>

          <motion.div
            class="rounded-lg bg-white/10 p-4"
            :initial="{ translateY: 20, opacity: 0 }"
            :animate="{ translateY: 0, opacity: 1 }"
            :transition="{ delay: 0.9, duration: 0.5 }"
          >
            <p class="mb-1 text-sm text-stone-700/70 dark:text-stone-300">
              風速
            </p>
            <p class="text-2xl font-semibold">
              {{ windSpeed }} m/s
            </p>
          </motion.div>

          <motion.div
            class="rounded-lg bg-white/10 p-4"
            :initial="{ translateY: 20, opacity: 0 }"
            :animate="{ translateY: 0, opacity: 1 }"
            :transition="{ delay: 1.2, duration: 0.5 }"
          >
            <p class="mb-1 text-sm text-stone-700/70 dark:text-stone-300">
              能見度
            </p>
            <p class="text-2xl font-semibold">
              {{ visibility }} km
            </p>
          </motion.div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
