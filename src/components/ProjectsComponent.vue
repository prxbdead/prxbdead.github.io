<script lang="ts">
interface Project {
  title: string
  description: string
  dateRange: string
  image: string
  demoUrl?: string
  sourceUrl?: string
  tags: string[]
}

export default {
  emits: ['updateCursor'],
  data() {
    return {
      projects: [
        {
          title: 'Mozisarok.hu',
          description:
            'A movie and series discovery platform where visitors can browse titles, check details, and keep track of what to watch next.',
          dateRange: '2023 — Present',
          image: new URL('@/assets/projects/mozisarok.webp', import.meta.url).href,
          demoUrl: 'https://mozisarok.hu',
          tags: ['Frontend', 'Backend', 'Entertainment']
        },
        {
          title: 'Aquantic.hu',
          description:
            'The website and store for a Minecraft server, handling everything from server info and community pages to in-game purchases.',
          dateRange: '2022 — 2025',
          image: new URL('@/assets/projects/aquantic.webp', import.meta.url).href,
          demoUrl: 'https://aquantic.hu',
          tags: ['Frontend', 'Backend', 'E-commerce']
        },
        {
          title: 'Dino CLI',
          description:
            'A retro-style endless runner game where players dodge obstacles to survive, featuring dynamic difficulty and a custom leaderboard powered by dynamic ordered lists.',
          dateRange: '2024',
          image: new URL('@/assets/projects/dino-cli.webp', import.meta.url).href,
          sourceUrl: 'https://github.com/prxbdead/dino-cli',
          tags: ['C++', 'Data Structures', 'Game Dev']
        }, 
        {
          title: 'BMP Editor',
          description:
            'A low-level BMP image editor written in assembly language, featuring a graphical interface for cropping, resizing, blurring, and applying RLE compression.',
          dateRange: '2024',
          image: new URL('@/assets/projects/bmp-editor.webp', import.meta.url).href,
          sourceUrl: 'https://github.com/prxbdead/bmp-editor',
          tags: ['Assembly', 'Image Processing', 'Systems']
        },
        {
          title: 'Shooter Game',
          description:
            'A Counter-Strike 2-inspired 3D game built from scratch in OpenTK, featuring custom collisions, physics, and skybox rendering.',
          dateRange: '2026',
          image: new URL('@/assets/projects/shooter.webp', import.meta.url).href,
          sourceUrl: 'https://github.com/prxbdead/shooter-project',
          tags: ['C#', 'OpenTK', 'Game Dev']
        },
      ] as Project[],
      visible: [] as boolean[]
    }
  },
  created() {
    this.visible = this.projects.map(() => false)
  },
  mounted() {
    const cards = this.$refs.card as HTMLElement[]
    if (!cards || !('IntersectionObserver' in window)) {
      this.visible = this.projects.map(() => true)
      return
    }

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            const index = Number((entry.target as HTMLElement).dataset.index)
            this.visible[index] = true
            observer.unobserve(entry.target)
          }
        })
      },
      { threshold: 0.2 }
    )

    cards.forEach((card) => observer.observe(card))
  },
  methods: {
    updateCursor(event: Event) {
      this.$emit('updateCursor', event)
    }
  }
}
</script>

<template>
  <section id="projects" class="projects">
    <h2 @mouseover="updateCursor" @mouseleave="updateCursor">projects</h2>
    <p class="intro" @mouseover="updateCursor" @mouseleave="updateCursor">
      A couple of things I've built and kept running.
    </p>

    <div class="grid">
      <article v-for="(project, index) in projects" :key="project.title" ref="card" :data-index="index" class="card"
        :class="{ visible: visible[index] }">
        <div class="thumb">
          <img :src="project.image" :alt="`${project.title} screenshot`" loading="lazy" />
        </div>
        <div class="info">
          <div class="heading">
            <h3 @mouseover="updateCursor" @mouseleave="updateCursor">{{ project.title }}</h3>
            <span class="date">{{ project.dateRange }}</span>
          </div>
          <p @mouseover="updateCursor" @mouseleave="updateCursor">{{ project.description }}</p>
          <div class="tags">
            <span v-for="tag in project.tags" :key="tag">{{ tag }}</span>
          </div>
          <a v-if="project.demoUrl" :href="project.demoUrl" target="_blank" rel="noopener noreferrer" class="demo-link"
            @mouseover="updateCursor" @mouseleave="updateCursor">
            <div>view live site →</div>
          </a>
          <a v-else-if="project.sourceUrl" :href="project.sourceUrl" target="_blank" rel="noopener noreferrer"
            class="demo-link" @mouseover="updateCursor" @mouseleave="updateCursor">
            <div>view source code →</div>
          </a>
        </div>
      </article>
    </div>
  </section>
</template>

<style scoped>
.projects {
  padding: 12vh 10vw 20vh;
  min-height: 100vh;
  scroll-snap-align: start;

  h2 {
    font-size: min(6vh, 12vw);
    font-weight: bolder;
    text-transform: lowercase;
  }

  .intro {
    font-size: min(2vh, 4vw);
    opacity: 0.75;
    max-width: 40em;
    margin: 1em 0 6vh;
  }
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 3em;
}

.card {
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  background: rgba(139, 92, 246, 0.04);
  border: 1px solid rgba(139, 92, 246, 0.12);
  pointer-events: all;

  opacity: 0;
  transform: translateY(24px);
  transition:
    opacity 0.6s ease-out,
    transform 0.6s ease-out,
    border-color 0.25s ease;
}

.card:hover {
  border-color: rgba(139, 92, 246, 0.28);
}

.card.visible {
  opacity: 1;
  transform: translateY(0);
}

@media (prefers-reduced-motion: reduce) {
  .card {
    transition: none;
    transform: none;
    opacity: 1;
  }
}

.thumb {
  aspect-ratio: 16 / 10;
  overflow: hidden;
  background: #150d2a;

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
    transition: transform 0.4s ease-out;
  }
}

.card:hover .thumb img {
  transform: scale(1.04);
}

.info {
  padding: 1.5em;
  display: flex;
  flex-direction: column;
  gap: 0.75em;
  flex: 1;

  .heading {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    gap: 1em;

    h3 {
      font-size: min(3vh, 5vw);
      font-weight: bold;
    }

    .date {
      font-size: 0.85em;
      opacity: 0.6;
      white-space: nowrap;
    }
  }

  p {
    font-size: min(1.8vh, 3.6vw);
    opacity: 0.85;
    line-height: 1.5;
    flex: 1;
  }

  .tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5em;
    align-items: flex-start;

    span {
      font-size: 0.75em;
      padding: 0.3em 0.8em;
      border-radius: 999px;
      background: rgba(139, 92, 246, 0.15);
      color: #c4b5fd;
    }
  }
}

.demo-link {
  margin-top: 0.5em;
  align-self: flex-start;
  font-weight: 600;
  color: #a78bfa;
  text-decoration: none;
  transition: color 0.2s ease;
  padding: 0.45em 0.9em;
  border-radius: 999px;
  margin-left: -0.9em;
}

.demo-link:hover {
  color: #c4b5fd;
}

.demo-link div {
  transition: transform 0.15s ease-out;
}

@media (prefers-reduced-motion: reduce) {
  .demo-link div {
    transition: none;
  }
}

@media screen and (max-width: 800px) {
  .grid {
    grid-template-columns: 1fr;
  }
}
</style>
