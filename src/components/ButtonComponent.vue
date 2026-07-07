<script lang="ts">
export default {
  props: {
    href: {
      type: String,
      default: null
    },
    variant: {
      type: String,
      default: 'default'
    },
    tabindex: {
      type: [String, Number],
      default: 0
    }
  },
  emits: ['updateCursor'],
  computed: {
    tag() {
      return this.href ? 'a' : 'button'
    },
    isExternal() {
      return typeof this.href === 'string' && /^https?:\/\//.test(this.href)
    }
  },
  methods: {
    onCursor(event: Event) {
      this.$emit('updateCursor', event)
    }
  }
}
</script>

<template>
  <component
    :is="tag"
    class="app-button"
    :class="variant"
    :href="href ?? undefined"
    :target="isExternal ? '_blank' : undefined"
    :rel="isExternal ? 'noopener noreferrer' : undefined"
    :tabindex="tabindex"
    @mouseover="onCursor"
    @mouseleave="onCursor"
  >
    <div class="app-button-inner">
      <slot name="icon"></slot>
      <slot></slot>
    </div>
  </component>
</template>

<style scoped>
.app-button {
  display: inline-flex;
  align-items: center;
  border: 1px solid rgba(139, 92, 246, 0.35);
  border-radius: 999px;
  padding: 0.7em 1.4em;
  font: inherit;
  color: inherit;
  background: rgba(139, 92, 246, 0.06);
  cursor: pointer;
  text-decoration: none;
  transition:
    border-color 0.2s ease,
    background 0.2s ease;
}

.app-button:hover {
  border-color: rgba(139, 92, 246, 0.6);
  background: rgba(139, 92, 246, 0.12);
}

.app-button.primary {
  background: #7c3aed;
  border-color: #7c3aed;
  color: #fff;
}

.app-button.primary:hover {
  background: #6d28d9;
  border-color: #6d28d9;
}

.app-button-inner {
  display: flex;
  align-items: center;
  gap: 0.6em;
  transition: transform 0.15s ease-out;
}

@media (prefers-reduced-motion: reduce) {
  .app-button-inner {
    transition: none;
  }
}
</style>