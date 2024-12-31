<script setup>
import { ref } from 'vue'
import ThemeToggle from './components/ThemeToggle.vue'
import GameMenu from './views/GameMenu.vue'
import WordleGame from './games/WordleGame.vue'
import NumberGuessingGame from './games/NumberGuessingGame.vue'
import BottleSpinGame from './games/BottleSpinGame.vue'

const selectedGame = ref(null)
const isDarkMode = ref(false)

const games = [
  {
    id: 'number-guess',
    title: 'Number Guessing',
    description: 'Test your intuition by guessing the right number',
    icon: '🎲'
  },
  {
    id: 'wordle',
    title: 'Wordle Game',
    description: 'Guess the hidden word with infinite tries',
    icon: '📝'
  },
  {
    id: 'bottle-spin',
    title: 'Bottle Spin',
    description: 'Spin the bottle and see what happens',
    icon: '🍾'
  },
  {
    id: 'coming-soon-1',
    title: 'Memory Match',
    description: 'Challenge your memory with this classic card game',
    icon: '🎮'
  },
  {
    id: 'coming-soon-2',
    title: 'Word Puzzle',
    description: 'Expand your vocabulary with word challenges',
    icon: '🎯'
  },
  {
    id: 'coming-soon-3',
    title: 'Math Quiz',
    description: 'Sharpen your math skills with quick calculations',
    icon: '🎪'
  }
]

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
}
</script>

<template>
  <div :class="[
    'min-h-screen transition-colors duration-50 font-sans',
    isDarkMode 
      ? 'bg-gray-900 text-white' 
      : 'bg-gray-50 text-gray-900'
  ]">
    <ThemeToggle 
      :isDarkMode="isDarkMode"
      @toggle="toggleTheme"
    />

    <div class="p-4 sm:p-6 md:p-8 max-w-7xl mx-auto">
      <GameMenu
        v-if="!selectedGame"
        :games="games"
        :isDarkMode="isDarkMode"
        @select-game="selectedGame = $event"
      />

      <NumberGuessingGame
        v-else-if="selectedGame === 'number-guess'"
        :isDarkMode="isDarkMode"
        @go-back="selectedGame = null"
      />

      <WordleGame
        v-else-if="selectedGame === 'wordle'"
        :isDarkMode="isDarkMode"
        @go-back="selectedGame = null"
      />

      <BottleSpinGame
        v-else-if="selectedGame === 'bottle-spin'"
        :isDarkMode="isDarkMode"
        @go-back="selectedGame = null"
      />

      <!-- Placeholder for other games -->
      <div v-else 
        :class="[
          'max-w-md mx-auto rounded-2xl shadow-lg backdrop-blur-sm p-6 sm:p-8 transition-all duration-50',
          isDarkMode 
            ? 'bg-gray-800/90 shadow-gray-900/30' 
            : 'bg-white/90 shadow-gray-200/70'
        ]"
      >
        <div class="flex flex-col sm:flex-row justify-between items-center mb-8 gap-4 sm:gap-0">
          <button 
            @click="selectedGame = null"
            :class="[
              'px-4 py-2 rounded-xl transition-all duration-50 flex items-center gap-2',
              isDarkMode 
                ? 'text-gray-300 hover:text-white hover:bg-gray-700/50' 
                : 'text-gray-600 hover:text-gray-800 hover:bg-gray-100/50'
            ]"
          >
            <span class="text-lg">←</span>
            <span>Back</span>
          </button>
          <h1 class="text-xl sm:text-2xl font-medium">Coming Soon</h1>
        </div>
        <p :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'" 
           class="text-center text-sm sm:text-base leading-relaxed">
          This game is currently under development. Check back soon!
        </p>
      </div>
    </div>
  </div>
</template>

<style>
/* Global styles */
:root {
  --app-blur: blur(20px);
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, 
               Helvetica, Arial, sans-serif, "Apple Color Emoji", 
               "Segoe UI Emoji", "Segoe UI Symbol";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Better button and input defaults */
button, input {
  font-family: inherit;
}

/* Remove tap highlight on mobile */
* {
  -webkit-tap-highlight-color: transparent;
}
</style>


