// eslint-disable-next-line vue/multi-word-component-names
<script setup lang="ts">
import ButtonComponent from './ButtonComponent.vue'
</script>

<script lang="ts">
export default {
  data() {
    return {
      birthDate: new Date(2004, 8, 16),
      age: 0
    }
  },
  emits: ['updateCursor'],
  methods: {
    updateCursor(event: Event) {
      this.$emit('updateCursor', event)
    },
    calculateAge() {
      const today = new Date()
      this.age = today.getFullYear() - this.birthDate.getFullYear()
      const monthDifference = today.getMonth() - this.birthDate.getMonth()
      const dayDifference = today.getDate() - this.birthDate.getDate()

      if (monthDifference < 0 || (monthDifference === 0 && dayDifference < 0)) {
        this.age--
      }
    },
    scrollToProjects() {
      document.getElementById('projects')?.scrollIntoView({ behavior: 'smooth' })
    }
  },
  created() {
    this.calculateAge()
  }
}
</script>

<template>
  <section id="home" class="header">
    <div class="left">
      <div class="top">
        <h3 @mouseover="updateCursor" @mouseleave="updateCursor">hey, my name is</h3>
        <h1 @mouseover="updateCursor" @mouseleave="updateCursor">Roland</h1>
      </div>
      <div class="center">
        <p @mouseover="updateCursor" @mouseleave="updateCursor">
          I'm a {{ age }}-year-old Computer Science graduate from Babeș-Bolyai University. I'm
          passionate about computers — from low-level systems and how hardware works, to writing
          software that's fast, clean, and correct. I enjoy optimization, learning how things
          really work under the hood, and building products that last.
        </p>
      </div>
      <div class="bottom">
        <ButtonComponent @updateCursor="updateCursor" @click="scrollToProjects">
          <template #icon>
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2">
              <rect x="3" y="4" width="18" height="16" rx="2" />
              <path d="M3 9h18" />
            </svg>
          </template>
          projects
        </ButtonComponent>

        <ButtonComponent href="https://github.com/prxbdead" @updateCursor="updateCursor">
          <template #icon>
            <img alt="github-svg" src="@/assets/icons/github.svg" />
          </template>
          github
        </ButtonComponent>

        <ButtonComponent href="mailto:roland.gerg.123@gmail.com" variant="primary" @updateCursor="updateCursor">
          <template #icon>
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2">
              <rect x="2" y="4" width="20" height="16" rx="2" />
              <path d="m2 6 10 7 10-7" />
            </svg>
          </template>
          hire me
        </ButtonComponent>
      </div>
    </div>
    <div class="right"></div>
  </section>
</template>

<style>
.header {
  display: flex;
  justify-content: center;
  align-items: center;

  height: 100vh;
  width: 100%;
  padding: 10vh 10vw;
  scroll-snap-align: start;

  .right {
    min-width: 30%;
  }

  .left {
    height: 100%;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: left;

    .top {
      justify-content: end;
      padding-bottom: 5vh;

      h1 {
        font-size: min(10vh, 20vw);
        font-weight: bolder;
        transform: translate(-0.08em, 0);
      }

      h3 {
        font-size: min(3vh, 6vw);
        font-weight: 100;
      }
    }

    .center {
      justify-content: center;
      max-width: 50vw;

      p {
        font-weight: normal;
        font-size: min(2vh, 4vw);
      }
    }

    .bottom {
      display: flex;
      flex-wrap: wrap;
      gap: 1em;
      justify-content: first;
      padding: 5vh 0;
    }
  }
}

@media screen and (max-width: 800px) {
  .header {
    .right {
      display: none;
    }

    .left .center {
      max-width: 100%;
    }
  }
}
</style>