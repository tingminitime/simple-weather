<script setup lang="ts">
defineOptions({
  name: 'IndexPage',
})

// const { data, error, isFetching } = useWeatherApi()
</script>

<template>
  <div>
    <!-- <div v-if="isFetching">
      Loading...
    </div>

    <div v-else-if="error">
      Error: {{ error.message }}
    </div> -->

    <WeatherCard />
  </div>
</template>

<style scope></style>
