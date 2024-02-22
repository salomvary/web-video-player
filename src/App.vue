<script lang="ts">
import { defineComponent } from 'vue'
import Help from '@/components/Help.vue'

const framesPerSecond = 25

export default defineComponent({
  components: { Help },
  data() {
    return {
      showHelp: false,
      message: 'Select a video file.\n\nPress ? for help.',
      paused: true,
      currentTime: 0,
      duration: 0,
      url: undefined as string | undefined
    }
  },

  computed: {
    framesPerSecond: () => framesPerSecond,

    formattedCurrentTime() {
      const seconds = Math.floor(this.currentTime)
      const milliseconds = Math.round((this.currentTime % 1) * 1000)
        .toString()
        .padStart(3, '0')
      return `${seconds}.${milliseconds}`
    }
  },

  mounted() {
    document.addEventListener('keyup', this.handleKeyUp)
  },

  beforeUnmount() {
    document.removeEventListener('keyup', this.handleKeyUp)
  },

  methods: {
    handleFileChange(event: Event) {
      const files = (event.target as HTMLInputElement).files
      if (files) {
        this.url = URL.createObjectURL(files[0])
      }
    },

    handleLoadMetadata() {
      const video = this.$refs.video as HTMLVideoElement
      this.duration = video.duration
    },

    handleTimeUpdate() {
      const video = this.$refs.video as HTMLVideoElement
      this.currentTime = video.currentTime
    },

    handlePlay() {
      this.paused = false
    },

    handlePause() {
      this.paused = true
    },

    handlePlayClick() {
      const video = this.$refs.video as HTMLVideoElement
      video.play()
    },

    handlePauseClick() {
      const video = this.$refs.video as HTMLVideoElement
      video.pause()
    },

    handleChangeCurrentTime() {
      const input = this.$refs.currentTime as HTMLInputElement
      const video = this.$refs.video as HTMLVideoElement
      video.currentTime = input.valueAsNumber
    },

    seekTo(newTime: number) {
      this.currentTime = newTime

      const video = this.$refs.video as HTMLVideoElement | undefined
      if (video) {
        video.currentTime = newTime
      }
    },

    seekBy(timeDelta: number) {
      const newTime = Math.min(Math.max(0, this.currentTime + timeDelta), this.duration)

      this.seekTo(newTime)
    },

    handleWheel(event: WheelEvent) {
      // Prevent history navigation or scrolling
      event.preventDefault()
      const timeDelta = -event.deltaX * (1 / framesPerSecond) * (event.shiftKey ? 0.1 : 1)
      this.seekBy(timeDelta)
    },

    handleKeyUp(event: KeyboardEvent) {
      switch (event.code) {
        case 'Space':
          this.handleSpaceKey()
          break
        case 'ArrowLeft':
          this.handleLeftKey(event)
          break
        case 'ArrowRight':
          this.handleRightKey(event)
          break
        case 'Escape':
          this.handleEscapeKey()
          break
      }

      switch (event.key) {
        case '?':
          this.handleQuestionmarkKey()
          break
      }
    },

    handleSpaceKey() {
      const video = this.$refs.video as HTMLVideoElement | undefined
      if (video) {
        if (video.paused) {
          video.play()
        } else {
          video.pause()
        }
      }
    },

    handleLeftKey(event: KeyboardEvent) {
      if (event.altKey) {
        this.seekTo(0)
      } else {
        this.seekBy(-1 / framesPerSecond)
      }
    },

    handleRightKey(event: KeyboardEvent) {
      if (event.altKey) {
        this.seekTo(this.duration)
      } else {
        this.seekBy(1 / framesPerSecond)
      }
    },

    handleQuestionmarkKey() {
      this.showHelp = !this.showHelp
    },

    handleEscapeKey() {
      this.showHelp = false
    }
  }
})
</script>

<template>
  <div
    class="app"
    @wheel="handleWheel"
    @keyup.space="handleSpaceKey"
    @keyup.left="handleLeftKey"
    @keyup.right="handleRightKey"
  >
    <main v-if="showHelp">
      <Help class="help" />
    </main>

    <main v-if="!showHelp">
      <video
        v-if="url"
        ref="video"
        :src="url"
        @loadedmetadata="handleLoadMetadata"
        @timeupdate="handleTimeUpdate"
        @play="handlePlay"
        @pause="handlePause"
      />
      <div v-if="!url" class="video-selector">
        <label class="video-selector-button">
          <input type="file" @change="handleFileChange" />
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-arrow-down"
            viewBox="0 0 16 16"
          >
            <path
              fill-rule="evenodd"
              d="M8 1a.5.5 0 0 1 .5.5v11.793l3.146-3.147a.5.5 0 0 1 .708.708l-4 4a.5.5 0 0 1-.708 0l-4-4a.5.5 0 0 1 .708-.708L7.5 13.293V1.5A.5.5 0 0 1 8 1"
            />
          </svg>
        </label>

        <p class="message" v-if="message">
          {{ message }}
        </p>
      </div>
    </main>
    <footer>
      <section class="controls">
        <button v-if="paused" aria-label="Play" @click="handlePlayClick" :disabled="!url">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-play-fill"
            viewBox="0 0 16 16"
          >
            <path
              d="m11.596 8.697-6.363 3.692c-.54.313-1.233-.066-1.233-.697V4.308c0-.63.692-1.01 1.233-.696l6.363 3.692a.802.802 0 0 1 0 1.393"
            />
          </svg>
        </button>
        <button v-if="!paused" aria-label="Pause" @click="handlePauseClick" :disabled="!url">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-pause-fill"
            viewBox="0 0 16 16"
          >
            <path
              d="M5.5 3.5A1.5 1.5 0 0 1 7 5v6a1.5 1.5 0 0 1-3 0V5a1.5 1.5 0 0 1 1.5-1.5m5 0A1.5 1.5 0 0 1 12 5v6a1.5 1.5 0 0 1-3 0V5a1.5 1.5 0 0 1 1.5-1.5"
            />
          </svg>
        </button>
      </section>

      <section class="current-time">
        <input
          ref="currentTime"
          type="range"
          min="0"
          :max="duration"
          :step="1 / framesPerSecond"
          :value="currentTime"
          @input="handleChangeCurrentTime"
        />
        <time>{{ formattedCurrentTime }}</time>
      </section>
    </footer>
  </div>
</template>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  background-color: #373737;
  color: #cdcdcd;
  font-family: sans-serif;
}

main {
  flex: 1 1 auto;
  background-color: #202020;
  display: flex;
  justify-content: center;
}

.help {
  align-self: center;
}

.video-selector {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.video-selector-button {
  padding: 20px;
  border-radius: 10px;
  position: relative;
  background: rgba(255, 255, 255, 0.2);
  display: flex;
}

.video-selector-button svg {
  width: 80px;
  height: 80px;
}

.video-selector input {
  opacity: 0;
  position: absolute;
  width: 100%;
  height: 100%;
}

.message {
  text-align: center;
  white-space: pre;
}

footer {
  border-top: 0.5px solid black;
}

.controls {
  display: flex;
  justify-content: center;
  padding: 0.5rem;
}

button {
  background: none;
  border: none;
  color: inherit;
  display: flex;
}

button svg {
  width: 40px;
  height: 40px;
}

.current-time {
  display: flex;
  padding: 0.5rem;
  gap: 0.5rem;
  align-items: center;
}

.current-time input {
  flex: 1 1 auto;
}
</style>
