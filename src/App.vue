<script lang="ts">
import { defineComponent } from 'vue'
import Help from '@/components/Help.vue'
import CurrentTimeDisplay from '@/components/CurrentTimeDisplay.vue'
import SkipToStartButton from '@/components/SkipToStartButton.vue'
import PlayButton from '@/components/PlayButton.vue'
import PauseButton from '@/components/PauseButton.vue'
import SkipToEndButton from '@/components/SkipToEndButton.vue'
import VideoSelectorButton from '@/components/VideoSelectorButton.vue'

const framesPerSecond = 25

const defaultMessage = 'Select a video file.\n\nPress ? for help.'

export default defineComponent({
  components: {
    VideoSelectorButton,
    SkipToEndButton,
    PauseButton,
    PlayButton,
    SkipToStartButton,
    CurrentTimeDisplay,
    Help
  },
  data() {
    return {
      showHelp: false,
      message: defaultMessage,
      paused: true,
      currentTime: 0,
      duration: 0,
      url: undefined as string | undefined
    }
  },

  computed: {
    framesPerSecond: () => framesPerSecond
  },

  mounted() {
    document.addEventListener('keydown', this.handleKeyDown)
  },

  beforeUnmount() {
    document.removeEventListener('keydown', this.handleKeyDown)
  },

  methods: {
    handleFileChange(event: Event) {
      const files = (event.target as HTMLInputElement).files
      if (files) {
        this.url = URL.createObjectURL(files[0])
        // Reset error message
        this.message = defaultMessage
      }
    },

    handleVideoError() {
      // TODO: show a message based on video.error.code:
      // https://developer.mozilla.org/en-US/docs/Web/API/MediaError/code#media_error_code_constants
      this.message = 'Can not play this file :('
      // Hide video
      this.url = undefined
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

    handleSkipToStartClick() {
      this.seekTo(0)
    },

    handleSkipToEndClick() {
      this.seekTo(this.duration)
    },

    handleInputCurrentTime() {
      const input = this.$refs.currentTime as HTMLInputElement
      this.seekTo(input.valueAsNumber, true)
    },

    handleChangeCurrentTime() {
      const input = this.$refs.currentTime as HTMLInputElement
      this.seekTo(input.valueAsNumber)
    },

    seekTo(newTime: number, fast = false) {
      this.currentTime = newTime

      const video = this.$refs.video as HTMLVideoElement | undefined
      if (video) {
        if (fast && 'fastSeek' in video) {
          // Fast seek with precision tradeoff, not supported by chrome
          // https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/fastSeek
          video.fastSeek(newTime)
        } else {
          video.currentTime = newTime
        }
      }
    },

    seekBy(timeDelta: number, fast = false) {
      const newTime = Math.min(Math.max(0, this.currentTime + timeDelta), this.duration)

      this.seekTo(newTime, fast)
    },

    handleWheel(event: WheelEvent) {
      // Prevent history navigation or scrolling
      event.preventDefault()
      const timeDelta = -event.deltaX * (1 / framesPerSecond) * (event.shiftKey ? 0.1 : 1)
      this.seekBy(timeDelta, true)
    },

    handleKeyDown(event: KeyboardEvent) {
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
        @error="handleVideoError"
        @timeupdate="handleTimeUpdate"
        @play="handlePlay"
        @pause="handlePause"
      />

      <div v-if="!url" class="video-selector">
        <VideoSelectorButton @change="handleFileChange" />

        <p class="message" v-if="message">
          {{ message }}
        </p>
      </div>
    </main>
    <footer>
      <section class="controls">
        <SkipToStartButton @click="handleSkipToStartClick" :disabled="!url" />
        <PlayButton v-if="paused" @click="handlePlayClick" :disabled="!url" />
        <PauseButton v-if="!paused" @click="handlePauseClick" :disabled="!url" />
        <SkipToEndButton @click="handleSkipToEndClick" :disabled="!url" />
      </section>

      <section class="current-time">
        <input
          ref="currentTime"
          type="range"
          min="0"
          :max="duration"
          :step="1 / framesPerSecond"
          :value="currentTime"
          @input="handleInputCurrentTime"
          @change="handleChangeCurrentTime"
        />
        <CurrentTimeDisplay :current-time="currentTime" />
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
  flex-direction: column;
  justify-items: center;
  justify-content: center;
  position: relative;
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

.message {
  text-align: center;
  white-space: pre;
}

video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

footer {
  border-top: 0.5px solid black;
  padding: 0.5rem;
}

.controls {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 5px;
}

.current-time {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.current-time input {
  flex: 1 1 auto;
}
</style>

<style>
.icon-button {
  background: none;
  border: none;
  color: inherit;
  display: flex;
  box-sizing: border-box;
  width: 45px;
  height: 45px;
  padding: 0;
}

.icon-button svg {
  height: 100%;
  flex: 1 1 auto;
}

.skip-button {
  width: 30px;
  height: 30px;
}
</style>
