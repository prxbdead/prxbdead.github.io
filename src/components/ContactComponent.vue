// eslint-disable-next-line vue/multi-word-component-names
<script setup lang="ts">
import ButtonComponent from './ButtonComponent.vue'
</script>

<script lang="ts">
export default {
  emits: ['updateCursor'],
  methods: {
    updateCursor(event: Event) {
      this.$emit('updateCursor', event)
    }
  }
}
</script>

<template>
  <section id="contact" class="contact">
    <h2 @mouseover="updateCursor" @mouseleave="updateCursor">let's talk</h2>
    <p @mouseover="updateCursor" @mouseleave="updateCursor">
      I've just graduated and I'm actively looking for my first full-time role. If you think I'd
      be a good fit for your team, I'd love to hear from you.
    </p>

    <ButtonComponent href="mailto:roland.gerg.123@gmail.com" variant="primary" @updateCursor="updateCursor">
      <template #icon>
        <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
          <rect x="2" y="4" width="20" height="16" rx="2" />
          <path d="m2 6 10 7 10-7" />
        </svg>
      </template>
      roland.gerg.123@gmail.com
    </ButtonComponent>

    <footer>
      <span>&copy; {{ new Date().getFullYear() }} Gergely Roland</span>
    </footer>
  </section>
</template>

<style scoped>
.contact {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 12vh 10vw;
  gap: 1.5em;
  scroll-snap-align: start;

  h2 {
    font-size: min(6vh, 12vw);
    font-weight: bolder;
    text-transform: lowercase;
  }

  p {
    max-width: 36em;
    font-size: min(2vh, 4vw);
    opacity: 0.8;
  }
}

.contact :deep(.app-button) {
  font-size: min(2.2vh, 4.4vw);
  font-weight: 600;
  padding: 0.9em 1.6em;
}

.contact :deep(.app-button.primary:hover) {
  transform: translateY(-2px);
  background: #5b21b6;
  border-color: #5b21b6;
}

footer {
  margin-top: 4vh;
  font-size: 0.85em;
  opacity: 0.5;
}
</style>