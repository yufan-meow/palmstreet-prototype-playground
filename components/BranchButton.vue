<template>
  <button
    class="branch-button"
    :class="buttonClassNames"
    :type="type"
    :disabled="isDisabled"
  >
    <slot />
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  variant: {
    type: String,
    default: 'primary',
    validator: (value) => ['primary', 'secondary', 'destructive'].includes(value),
  },
  size: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md'].includes(value),
  },
  state: {
    type: String,
    default: 'default',
    validator: (value) => ['default', 'hovered', 'focused', 'disabled'].includes(value),
  },
  disabled: {
    type: Boolean,
    default: false,
  },
  type: {
    type: String,
    default: 'button',
  },
})

const isDisabled = computed(() => props.disabled || props.state === 'disabled')

const buttonClassNames = computed(() => [
  `branch-button--${props.variant}`,
  `branch-button--${props.size}`,
  props.state !== 'default' ? `branch-button--${props.state}` : null,
])
</script>

<style scoped>
.branch-button {
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: fit-content;
  border: 1px solid transparent;
  border-radius: 8px;
  font-family: 'Gabarito', sans-serif;
  font-weight: 500;
  line-height: 1;
  white-space: nowrap;
  user-select: none;
  cursor: pointer;
  transition: box-shadow 120ms ease, opacity 120ms ease, background-color 120ms ease, border-color 120ms ease;
}

.branch-button::after {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  opacity: 0;
  pointer-events: none;
  transition: opacity 120ms ease;
}

.branch-button--primary {
  background: var(--section-cta);
  color: var(--content-always-black);
}

.branch-button--secondary {
  background: var(--section-floor-1);
  border-color: var(--border);
  color: var(--content-primary);
}

.branch-button--destructive {
  background: var(--content-destructive);
  color: var(--content-always-white);
}

.branch-button--sm {
  height: 32px;
  padding: 0 12px;
  font-size: 14px;
}

.branch-button--md {
  height: 40px;
  padding: 0 16px;
  font-size: 16px;
}

.branch-button:hover::after,
.branch-button--hovered::after {
  background: rgba(0, 0, 0, 0.08);
  opacity: 1;
}

.branch-button:focus-visible,
.branch-button--focused {
  outline: none;
  box-shadow: 0 0 0 3px rgba(34, 105, 236, 0.24);
}

.branch-button:disabled,
.branch-button--disabled {
  opacity: 0.4;
  cursor: default;
  pointer-events: none;
}
</style>
