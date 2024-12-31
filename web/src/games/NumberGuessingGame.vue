<script setup>
import { ref } from 'vue'

const props = defineProps({
  isDarkMode: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['go-back'])

const targetNumber = ref(Math.floor(Math.random() * 100) + 1)
const userGuess = ref('')
const message = ref('')
const attempts = ref(0)
const gameWon = ref(false)

const checkGuess = () => {
  if (!userGuess.value) return
  
  attempts.value++
  const guess = parseInt(userGuess.value)
  
  if (guess === targetNumber.value) {
    message.value = `🎉 Congratulations! You won in ${attempts.value} attempts!`
    gameWon.value = true
  } else if (guess < targetNumber.value) {
    message.value = '📈 Too low! Try a higher number.'
  } else {
    message.value = '📉 Too high! Try a lower number.'
  }
  
  userGuess.value = ''
}

const resetGame = () => {
  targetNumber.value = Math.floor(Math.random() * 100) + 1
  userGuess.value = ''
  message.value = ''
  attempts.value = 0
  gameWon.value = false
}
</script>

<template>
  <div 
    :class="[
      'max-w-md mx-auto rounded-2xl shadow-lg p-6 sm:p-8 transition-colors duration-50',
      isDarkMode ? 'bg-gray-800 shadow-gray-900/30' : 'bg-white shadow-gray-200/70'
    ]"
  >
    <!-- Header Section -->
    <div class="flex flex-col sm:flex-row justify-between items-center mb-8 gap-4 sm:gap-0">
      <button 
        @click="$emit('go-back')"
        :class="[
          'px-4 py-2 rounded-lg transition-all duration-50 flex items-center gap-2',
          isDarkMode 
            ? 'text-gray-300 hover:text-white hover:bg-gray-700' 
            : 'text-gray-600 hover:text-gray-800 hover:bg-gray-100'
        ]"
      >
        <span class="text-lg">←</span>
        <span>Back</span>
      </button>
      <div class="px-4 py-2 rounded-lg bg-blue-500/10 text-blue-500">
        Attempts: {{ attempts }}
      </div>
    </div>

    <!-- Game Title -->
    <div class="text-center mb-8">
      <h1 class="text-2xl sm:text-3xl font-bold mb-3">Number Guessing Game</h1>
      <p :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'" class="text-sm sm:text-base">
        Try to guess the number between 1 and 100
      </p>
    </div>

    <!-- Game Input Section -->
    <div class="space-y-6">
      <div class="relative">
        <input
          type="number"
          inputmode="numeric"
          pattern="[0-9]*"
          v-model="userGuess"
          :disabled="gameWon"
          @keyup.enter="checkGuess"
          placeholder="Enter your guess..."
          :class="[
            'w-full px-6 py-4 rounded-xl text-lg transition-all duration-50 border-2',
            'focus:ring-4 outline-none',
            isDarkMode 
              ? 'bg-gray-700 border-gray-600 text-white placeholder-gray-400 focus:border-blue-500 focus:ring-blue-500/20'
              : 'bg-white border-gray-200 focus:border-blue-500 focus:ring-blue-500/20'
          ]"
        >
      </div>

      <!-- Action Buttons -->
      <div class="flex flex-col sm:flex-row gap-3">
        <button
          @click="checkGuess"
          :disabled="gameWon"
          class="flex-1 px-6 py-4 rounded-xl text-white text-base font-semibold transition-all duration-50
                 bg-blue-500 hover:bg-blue-600 disabled:opacity-50 transform hover:scale-102 hover:shadow-lg
                 disabled:hover:scale-100 disabled:hover:shadow-none"
        >
          Submit Guess
        </button>

        <button
          @click="resetGame"
          class="flex-1 px-6 py-4 rounded-xl text-base font-semibold transition-all duration-50
                 text-white bg-gray-500 hover:bg-gray-600 transform hover:scale-102 hover:shadow-lg"
        >
          New Game
        </button>
      </div>

      <!-- Message Display -->
      <div v-if="message" 
        :class="[
          'p-6 rounded-xl text-center text-base transition-all duration-50 transform',
          gameWon 
            ? (isDarkMode ? 'bg-green-900/50 text-green-200' : 'bg-green-100 text-green-700')
            : (isDarkMode ? 'bg-blue-900/50 text-blue-200' : 'bg-blue-100 text-blue-700')
        ]"
      >
        <p class="font-medium">{{ message }}</p>
        <p v-if="gameWon" class="text-sm mt-2 opacity-80">
          Click "New Game" to play again!
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  -moz-appearance: textfield;
}

/* Add smooth hover transitions */
button {
  transition: all 0.2s ease;
}

/* Improve focus states */
button:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 2px;
}
</style> 