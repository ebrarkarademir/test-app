<script setup>
defineProps({
  isDarkMode: Boolean
})

defineEmits(['toggle'])
</script>

<template>
  <button 
    @click="$emit('toggle')"
    :class="[
      'fixed top-4 right-4 p-3 rounded-full shadow-lg transition-all duration-50 backdrop-blur-sm',
      isDarkMode 
        ? 'bg-gray-800/90 text-yellow-400 hover:bg-gray-700/90' 
        : 'bg-white/90 text-gray-800 hover:bg-gray-50/90'
    ]"
  >
    <span class="text-xl">{{ isDarkMode ? '☀️' : '🌙' }}</span>
  </button>
</template> 