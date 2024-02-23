<template>
  <button class="icon-button skip-button" aria-label="Skip to end">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="16"
      height="16"
      fill="currentColor"
      class="bi bi-skip-end-fill"
      viewBox="0 0 16 16"
    >
      <path
        d="M12.5 4a.5.5 0 0 0-1 0v3.248L5.233 3.612C4.693 3.3 4 3.678 4 4.308v7.384c0 .63.692 1.01 1.233.697L11.5 8.753V12a.5.5 0 0 0 1 0z"
      />
    </svg>
  </button>
</template>
<script lang="ts">
export default {
  name: 'SkipToEndButton',
  props: {
    handleSkipToEndClick: {},
    url: {}
  }
}
</script>
<style scoped>
main {
  flex: 1 1 auto;
  background-color: #202020;
  display: flex;
  flex-direction: column;
  justify-items: center;
  justify-content: center;
  position: relative;
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
