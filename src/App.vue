<script setup lang="ts">
import Header from './components/HeaderComponent.vue'
import Projects from './components/ProjectsComponent.vue'
import Contact from './components/ContactComponent.vue'
import Cursor from './components/CursorComponent.vue'
import NavButtons from './components/NavButtonsComponent.vue'
</script>

<script lang="ts">
interface Particle {
  x: number
  y: number
  vx: number
  vy: number
  radius: number
  alpha: number
}

export default {
  data() {
    return {
      cursor: {
        class: '',
        x: 0,
        y: 0,
        h: 32,
        w: 32
      },
      hoveredEl: null as HTMLElement | null,
      ticking: false,
      particles: [] as Particle[],
      animationFrameId: 0,
      mousePos: { x: -9999, y: -9999 },
      canvasSize: { width: 0, height: 0 }
    }
  },
  mounted() {
    this.initCanvas()
    window.addEventListener('resize', this.handleResize)
  },
  beforeUnmount() {
    if (this.animationFrameId) cancelAnimationFrame(this.animationFrameId)
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    updateCursor(event: Event) {
      const target = event.target as HTMLElement
      const buttonEl = target.closest('.app-button, .demo-link') as HTMLElement | null

      if (buttonEl) {
        if (event.type === 'mouseover') {
          const rect = buttonEl.getBoundingClientRect()
          this.cursor.class = 'hover'
          this.cursor.h = rect.height
          this.cursor.w = rect.width
          this.cursor.x = (rect.left + rect.right) / 2
          this.cursor.y = (rect.top + rect.bottom) / 2
          buttonEl.classList.add('hover')
          this.hoveredEl = buttonEl
        } else {
          this.cursor.class = ''
          this.cursor.h = 32
          this.cursor.w = 32
          buttonEl.classList.remove('hover')

          const child = buttonEl.firstElementChild as HTMLElement
          if (child) child.style.transform = ''

          if (this.hoveredEl === buttonEl) this.hoveredEl = null
        }
      } else {
        if (event.type === 'mouseover') {
          this.cursor.class = 'hover-text'
          this.cursor.h = parseInt(window.getComputedStyle(target).fontSize, 10) * 1.5
        } else {
          this.cursor.class = ''
          this.cursor.h = 32
          this.cursor.w = 32
        }
      }
    },

    updateMousePosition(event: MouseEvent) {
      this.mousePos.x = event.clientX
      this.mousePos.y = event.clientY

      if (this.ticking) return
      this.ticking = true

      requestAnimationFrame(() => {
        const root = document.documentElement
        root.style.setProperty('--bgoffsetx', (event.clientX / window.innerWidth) * 50 + 'px')
        root.style.setProperty('--bgoffsety', (event.clientY / window.innerHeight) * 50 + 'px')

        if (this.cursor.class === 'hover' && this.hoveredEl) {
          const rect = this.hoveredEl.getBoundingClientRect()
          const child = this.hoveredEl.firstElementChild as HTMLElement
          if (child) {
            child.style.transform =
              'translate(' +
              ((event.clientX - rect.left) / this.cursor.w - 0.5) * 20 +
              '%, ' +
              ((event.clientY - rect.top) / this.cursor.h - 0.5) * 20 +
              '%)'
          }
        } else {
          this.cursor.x = event.clientX
          this.cursor.y = event.clientY
        }

        this.ticking = false
      })
    },

    initCanvas() {
      const canvas = this.$refs.canvas as HTMLCanvasElement
      if (!canvas) return
      this.resizeCanvas()
      this.createParticles()
      this.animateParticles()
    },

    resizeCanvas() {
      const canvas = this.$refs.canvas as HTMLCanvasElement
      if (!canvas) return
      this.canvasSize.width = window.innerWidth
      this.canvasSize.height = window.innerHeight
      canvas.width = this.canvasSize.width
      canvas.height = this.canvasSize.height
    },

    handleResize() {
      this.resizeCanvas()
      this.createParticles()
    },

    createParticles() {
      const count = Math.min(55, Math.floor((this.canvasSize.width * this.canvasSize.height) / 22000))
      const particles: Particle[] = []
      for (let i = 0; i < count; i++) {
        particles.push({
          x: Math.random() * this.canvasSize.width,
          y: Math.random() * this.canvasSize.height,
          vx: (Math.random() - 0.5) * 0.25,
          vy: (Math.random() - 0.5) * 0.25,
          radius: Math.random() * 1.2 + 0.8,
          alpha: Math.random() * 0.3 + 0.08
        })
      }
      this.particles = particles
    },

    animateParticles() {
      const canvas = this.$refs.canvas as HTMLCanvasElement
      if (!canvas) return
      const ctx = canvas.getContext('2d')
      if (!ctx) return

      ctx.clearRect(0, 0, this.canvasSize.width, this.canvasSize.height)

      const maxDist = 130
      const n = this.particles.length

      for (let i = 0; i < n; i++) {
        const p = this.particles[i]

        p.x += p.vx
        p.y += p.vy

        if (p.x < 0 || p.x > this.canvasSize.width) p.vx *= -1
        if (p.y < 0 || p.y > this.canvasSize.height) p.vy *= -1

        const dxM = this.mousePos.x - p.x
        const dyM = this.mousePos.y - p.y
        const dM = Math.sqrt(dxM * dxM + dyM * dyM)
        if (dM < 160 && dM > 0) {
          const f = (160 - dM) / 3200
          p.x -= dxM * f
          p.y -= dyM * f
        }

        ctx.beginPath()
        ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2)
        ctx.fillStyle = `rgba(168, 85, 247, ${p.alpha})`
        ctx.fill()

        for (let j = i + 1; j < n; j++) {
          const q = this.particles[j]
          const dx = p.x - q.x
          const dy = p.y - q.y
          const d = Math.sqrt(dx * dx + dy * dy)
          if (d < maxDist) {
            ctx.beginPath()
            ctx.moveTo(p.x, p.y)
            ctx.lineTo(q.x, q.y)
            ctx.strokeStyle = `rgba(139, 92, 246, ${(1 - d / maxDist) * 0.1})`
            ctx.lineWidth = 0.5
            ctx.stroke()
          }
        }

        const dxMouse = p.x - this.mousePos.x
        const dyMouse = p.y - this.mousePos.y
        const dMouse = Math.sqrt(dxMouse * dxMouse + dyMouse * dyMouse)
        if (dMouse < 140) {
          ctx.beginPath()
          ctx.moveTo(p.x, p.y)
          ctx.lineTo(this.mousePos.x, this.mousePos.y)
          ctx.strokeStyle = `rgba(167, 139, 250, ${(1 - dMouse / 140) * 0.18})`
          ctx.lineWidth = 0.6
          ctx.stroke()
        }
      }

      this.animationFrameId = requestAnimationFrame(this.animateParticles)
    }
  }
}
</script>

<template>
  <div id="container" @mousemove="updateMousePosition">
    <div id="bg">
      <div class="blob blob-1"></div>
      <div class="blob blob-2"></div>
      <div class="blob blob-3"></div>
      <div class="grid-overlay"></div>
      <canvas ref="canvas" class="particle-canvas"></canvas>
    </div>

    <Cursor :cursor="cursor"></Cursor>
    <NavButtons @updateCursor="updateCursor"></NavButtons>
    <Header @updateCursor="updateCursor"></Header>
    <Projects @updateCursor="updateCursor"></Projects>
    <Contact @updateCursor="updateCursor"></Contact>
  </div>
</template>

<style scoped>
#container {
  height: 100vh;
  pointer-events: all;
  overflow-y: scroll;
  scroll-snap-type: y mandatory;
  position: relative;
}

#bg {
  position: fixed;
  inset: 0;
  z-index: -1;
  pointer-events: none;
  overflow: hidden;
  background: #07020f;
}

.blob {
  position: absolute;
  border-radius: 50%;
  filter: blur(110px);
  opacity: 0.18;
  mix-blend-mode: screen;
  pointer-events: none;
}

.blob-1 {
  width: 55vw;
  height: 55vw;
  top: -15%;
  left: -10%;
  background: radial-gradient(circle, #7c3aed 0%, transparent 70%);
  animation: drift-a 28s infinite alternate ease-in-out;
  transform: translate(
    calc(var(--bgoffsetx) * -0.4),
    calc(var(--bgoffsety) * -0.4)
  );
  will-change: transform;
}

.blob-2 {
  width: 65vw;
  height: 65vw;
  bottom: -15%;
  right: -15%;
  background: radial-gradient(circle, #4f46e5 0%, transparent 70%);
  animation: drift-b 35s infinite alternate ease-in-out;
  transform: translate(
    calc(var(--bgoffsetx) * 0.35),
    calc(var(--bgoffsety) * 0.35)
  );
  will-change: transform;
}

.blob-3 {
  width: 40vw;
  height: 40vw;
  top: 35%;
  left: 38%;
  background: radial-gradient(circle, #6d28d9 0%, transparent 70%);
  animation: drift-c 22s infinite alternate ease-in-out;
  transform: translate(
    calc(var(--bgoffsetx) * -0.2),
    calc(var(--bgoffsety) * -0.2)
  );
  will-change: transform;
}

.grid-overlay {
  position: absolute;
  inset: -60px;
  pointer-events: none;
  transform: translate(
    calc(var(--bgoffsetx) * 0.15),
    calc(var(--bgoffsety) * 0.15)
  );
  will-change: transform;
  background-image:
    linear-gradient(to right, rgba(139, 92, 246, 0.04) 1px, transparent 1px),
    linear-gradient(to bottom, rgba(139, 92, 246, 0.04) 1px, transparent 1px);
  background-size: 64px 64px;
}

.particle-canvas {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

@keyframes drift-a {
  0%   { transform: translate(0, 0) scale(1); }
  50%  { transform: translate(4%, 8%) scale(1.08); }
  100% { transform: translate(-4%, -4%) scale(0.94); }
}

@keyframes drift-b {
  0%   { transform: translate(0, 0) scale(0.92); }
  50%  { transform: translate(-8%, -4%) scale(1.12); }
  100% { transform: translate(4%, 8%) scale(1); }
}

@keyframes drift-c {
  0%   { transform: translate(0, 0) scale(1.08); }
  50%  { transform: translate(6%, -6%) scale(0.96); }
  100% { transform: translate(-6%, 6%) scale(1.04); }
}

@media (prefers-reduced-motion: reduce) {
  #container {
    scroll-snap-type: none;
  }

  .blob {
    animation: none;
  }
}
</style>
