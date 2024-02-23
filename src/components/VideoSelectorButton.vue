<template>
  <label class="video-selector-button">
    <input type="file" />
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
</template>

<style scoped>
.video-selector-button {
  padding: 20px;
  border-radius: 10px;
  position: relative;
  background: rgba(255, 255, 255, 0.2);
  display: flex;
}

.video-selector-button:focus-within {
  outline: 2px solid dodgerblue;
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
</style>
