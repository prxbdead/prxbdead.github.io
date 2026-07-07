<script lang="ts">
const SECTIONS = ['home', 'projects', 'contact']

export default {
  emits: ['updateCursor'],
  data() {
    return {
      currentIndex: 0,
      observer: null as IntersectionObserver | null
    }
  },
  computed: {
    canGoUp(): boolean {
      return this.currentIndex > 0
    },
    canGoDown(): boolean {
      return this.currentIndex < SECTIONS.length - 1
    }
  },
  mounted() {
    this.observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            const id = entry.target.id
            const idx = SECTIONS.indexOf(id)
            if (idx !== -1) this.currentIndex = idx
          }
        })
      },
      {
        root: document.getElementById('container'),
        threshold: 0.1
      }
    )
    SECTIONS.forEach((id) => {
      const el = document.getElementById(id)
      if (el) this.observer!.observe(el)
    })
  },
  beforeUnmount() {
    this.observer?.disconnect()
  },
  methods: {
    navigate(dir: 1 | -1) {
      this.navigateTo(this.currentIndex + dir)
    },
    navigateTo(index: number) {
      if (index < 0 || index >= SECTIONS.length) return
      const el = document.getElementById(SECTIONS[index])
      if (el) el.scrollIntoView({ behavior: 'smooth' })
    },
    updateCursor(event: Event) {
      this.$emit('updateCursor', event)
    }
  }
}
</script>

<template>
  <nav class="nav-buttons" aria-label="Section navigation">
    <button
      class="nav-btn app-button"
      :class="{ dimmed: !canGoUp }"
      aria-label="Scroll to previous section"
      @click="navigate(-1)"
      @mouseover="updateCursor"
      @mouseleave="updateCursor"
    >
      <div>
        <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M18 15l-6-6-6 6" />
        </svg>
      </div>
    </button>

    <div class="dots" aria-hidden="true">
      <div
        v-for="(_, i) in 3"
        :key="i"
        class="dot"
        :class="{ active: currentIndex === i }"
      />
    </div>

    <button
      class="nav-btn app-button"
      :class="{ dimmed: !canGoDown }"
      aria-label="Scroll to next section"
      @click="navigate(1)"
      @mouseover="updateCursor"
      @mouseleave="updateCursor"
    >
      <div>
        <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M6 9l6 6 6-6" />
        </svg>
      </div>
    </button>
  </nav>
</template>

<style scoped>
.nav-buttons {
  position: fixed;
  right: 2rem;
  top: 50%;
  transform: translateY(-50%);
  z-index: 100;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.6rem;
  pointer-events: all;
}

.nav-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 1px solid rgba(139, 92, 246, 0.3);
  background: rgba(7, 2, 15, 0.6);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  color: #a78bfa;
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: all;

  /* smooth transition for all state changes */
  transition:
    opacity 0.4s cubic-bezier(0.4, 0, 0.2, 1),
    filter 0.4s cubic-bezier(0.4, 0, 0.2, 1),
    border-color 0.2s ease,
    background 0.2s ease,
    color 0.2s ease;
}

/* the inner div exists for the magnetic cursor effect — keep it centered */
.nav-btn > div {
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 0;
}

/* dimmed state: still visible but clearly inactive */
.nav-btn.dimmed {
  opacity: 0.22;
  filter: saturate(0.3);
  pointer-events: none;
}

/* hover is handled by the cursor system via .app-button,
   but we still want a subtle glow for non-cursor devices */
.nav-btn:not(.dimmed):hover {
  border-color: rgba(139, 92, 246, 0.6);
  background: rgba(124, 58, 237, 0.2);
  color: #c4b5fd;
}

.dots {
  display: flex;
  flex-direction: column;
  gap: 0.45rem;
  padding: 0.3rem 0;
  pointer-events: none;
}

.dot {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: rgba(139, 92, 246, 0.3);
  transition:
    background 0.35s cubic-bezier(0.4, 0, 0.2, 1),
    transform 0.35s cubic-bezier(0.4, 0, 0.2, 1);
  pointer-events: none;
}

.dot.active {
  background: #a78bfa;
  transform: scale(1.6);
}

@media (max-width: 800px) {
  /* hide arrows on mobile — dots stay as position indicators */
  .nav-btn {
    display: none;
  }

  .nav-buttons {
    right: 0.75rem;
    gap: 0.4rem;
  }
}

@media (prefers-reduced-motion: reduce) {
  .nav-btn,
  .dot {
    transition: none;
  }
}
</style>
