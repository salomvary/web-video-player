<template>
  <time>{{ formattedCurrentTime }}</time>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'CurrentTimeDisplay',
  props: {
    currentTime: {
      type: Number,
      required: true
    }
  },
  computed: {
    formattedCurrentTime() {
      const seconds = Math.floor(this.currentTime)
      const milliseconds = Math.round((this.currentTime % 1) * 1000)
        .toString()
        .padStart(3, '0')
      return `${seconds}.${milliseconds}`
    }
  }
})
</script>
