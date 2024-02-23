<template>
  <article>
    <h1>Keyboard shortcuts</h1>
    <dl>
      <dt>Space</dt>
      <dd>Toggle playback</dd>

      <dt>Left arrow</dt>
      <dd>Back by one frame</dd>

      <dt>Right arrow</dt>
      <dd>Forward by one frame</dd>

      <dt>Alt + left arrow</dt>
      <dd>Seek to start</dd>

      <dt>Alt + right arrow</dt>
      <dd>Seek to end</dd>

      <dt>?</dt>
      <dd>Toggle help</dd>
    </dl>
    <p>
      <small>Press Esc or ? to hide help.</small>
    </p>
    <p>
      <a href="https://github.com/salomvary/web-video-player/">Source code on GitHub.</a>
    </p>
  </article>
</template>

<style scoped>
h1 {
  border-bottom: 1px solid currentColor;
}

dt {
  font-weight: bold;
  margin-bottom: 0.3em;
}

dd {
  margin-bottom: 0.7em;
}
</style>
