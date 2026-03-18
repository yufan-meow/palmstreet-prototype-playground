<template>
  <div>
    <NuxtPage />
  </div>
</template>

<script setup>
// Persist theme preference across sessions
const theme = ref('light')

onMounted(() => {
  const saved = localStorage.getItem('ps-theme') || 'light'
  theme.value = saved
  document.documentElement.setAttribute('data-theme', saved)
})

provide('theme', theme)
provide('toggleTheme', () => {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
  document.documentElement.setAttribute('data-theme', theme.value)
  localStorage.setItem('ps-theme', theme.value)
})
</script>
