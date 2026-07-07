<script lang="ts">
export default {
  props: {
    cursor: {
      type: Object,
      default: () => ({
        class: '',
        x: 0,
        y: 0,
        h: 32,
        w: 32
      })
    }
  },
  computed: {
    cursorStyle() {
      return {
        top: `${this.cursor.y}px`,
        left: `${this.cursor.x}px`,
        height: `${this.cursor.h}px`,
        width: `${this.cursor.w}px`
      }
    }
  }
}
</script>

<template>
  <div id="cursor" :class="[cursor.class]" :style="cursorStyle"></div>
</template>

<style scoped>
#cursor {
  display: block;
  transform: translate(-50%, -50%);
  position: fixed;
  border-radius: 50%;
  pointer-events: none;
  transition:
    top 0.08s ease-out,
    left 0.08s ease-out,
    width 0.15s ease-out,
    height 0.15s ease-out,
    border-radius 0.15s ease-out,
    background 0.15s ease-out,
    opacity 0.15s ease-out;
  background: rgba(185, 139, 255, 0.25);
  border: 1px solid rgba(185, 139, 255, 0.5);
  opacity: 1;
  z-index: 9999;
}

#cursor.hover {
  border-radius: 3em;
  background: rgba(127, 0, 255, 0.12);
  border-color: rgba(127, 0, 255, 0.4);
  z-index: 0;
}

#cursor.hover-text {
  border-radius: 2px;
  width: 2px !important;
  background: #a855f7;
  border-color: transparent;
}

@media not (pointer: fine) {
  #cursor {
    display: none;

    &.hover {
      display: block;
    }
  }
}
</style>
