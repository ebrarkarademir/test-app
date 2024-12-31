<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
  isDarkMode: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['go-back'])

// Game state
const gameState = ref('setup') // setup, playing
const players = ref([])
const playerCount = ref(null)
const currentPlayerIndex = ref(0)
const isSpinning = ref(false)
const selectedPlayer = ref(null)
const selectedChoice = ref(null) // 'truth' or 'dare'
const rotationDegree = ref(0)
const playerNames = ref([])
const currentQuestion = ref('')

// Örnek soru ve görevler
const truthQuestions = [
  'Hayatında yaptığın en utanç verici şey neydi?',
  'En son kime yalan söyledin ve neden?',
  'Şimdiye kadar yaptığın en büyük yaramazlık neydi?',
  'Gizlice hoşlandığın biri var mı?',
  'Arkadaşına söylediğin en büyük yalan neydi?',
  'En büyük korkunun ne olduğunu söyle',
  'Hiç bir sırrı ifşaladın mı?',
  'En çok utandığın anını anlat',
  'Hiç bir şey çaldın mı?',
  'En son ne zaman ağladın?'
]

const dareActions = [
  'Bir dakika boyunca tavuk gibi davran',
  'Gruptaki birine aşk ilanı yap',
  'En kötü şarkını söyle',
  'Komik bir dans yap',
  'Bir dakika boyunca tek ayak üstünde dur',
  'Telefonundan son gönderdiğin mesajı yüksek sesle oku',
  'Gruba en çok utandığın fotoğrafını göster',
  'Bir sonraki tura kadar her cümlenin sonuna "miyav" ekle',
  'Gruptaki birinin taklitini yap',
  'Bir dakika boyunca robot gibi konuş'
]


// When player count is selected
const selectPlayerCount = (count) => {
  playerCount.value = count
  // Initialize empty array with selected count
  playerNames.value = Array(count).fill('')
}

// Check if all names are filled
const areAllNamesFilled = computed(() => {
  return playerNames.value.every(name => name.trim() !== '') && 
         playerNames.value.length === playerCount.value
})

// Start game with entered names
const startGame = () => {
  if (areAllNamesFilled.value) {
    players.value = [...playerNames.value]
    gameState.value = 'playing'
  }
}

// Rastgele soru/görev seçme
const getRandomItem = (array) => {
  return array[Math.floor(Math.random() * array.length)]
}

// Şişeyi döndürme
const spinBottle = () => {
  if (isSpinning.value) return
  
  isSpinning.value = true
  selectedChoice.value = null
  
  // Rastgele dönüş derecesi (720° ile 1440° arası + oyuncu pozisyonu için ek derece)
  const minSpins = 2
  const maxSpins = 4
  const baseRotation = (Math.random() * (maxSpins - minSpins) + minSpins) * 360
  const playerAngle = (Math.floor(Math.random() * players.value.length)) * (360 / players.value.length)
  
  rotationDegree.value = baseRotation + playerAngle
  
  setTimeout(() => {
    isSpinning.value = false
    selectedPlayer.value = Math.floor((rotationDegree.value % 360) / (360 / players.value.length))
  }, 3000)
}

// Seçim yapma
const makeChoice = (choice) => {
  selectedChoice.value = choice
  if (choice === 'truth') {
    currentQuestion.value = getRandomItem(truthQuestions)
  } else {
    currentQuestion.value = getRandomItem(dareActions)
  }
}

// Oyunu sıfırlama
const resetGame = () => {
  gameState.value = 'setup'
  players.value = []
  playerCount.value = null
  playerNames.value = []
  selectedPlayer.value = null
  selectedChoice.value = null
  rotationDegree.value = 0
  currentQuestion.value = ''
}

// Add array of colors for sections
const sectionColors = [
  'bg-blue-500',
  'bg-green-500',
  'bg-yellow-500',
  'bg-purple-500',
  'bg-pink-500',
  'bg-orange-500'
]

// Calculate section styles
const getSectionStyle = (index, total) => {
  const degree = 360 / total
  const rotation = index * degree
  return {
    transform: `rotate(${rotation}deg)`,
    width: '50%',
    height: '50%',
    transformOrigin: '100% 100%',
    position: 'absolute',
    left: '0',
    top: '0',
    clipPath: `polygon(100% 0, 100% 100%, 0 100%)`
  }
}

</script>

<template>
  <div 
    :class="[
      'max-w-2xl mx-auto rounded-2xl shadow-lg p-6 sm:p-8 transition-colors duration-50 backdrop-blur-sm',
      isDarkMode ? 'bg-gray-800/90 shadow-gray-900/30' : 'bg-white/90 shadow-gray-200/70'
    ]"
  >
    <!-- Header -->
    <div class="flex justify-between items-center mb-8">
      <button 
        @click="$emit('go-back')"
        class="px-4 py-2 rounded-lg transition-all duration-50 flex items-center gap-2"
        :class="isDarkMode ? 'text-gray-300 hover:text-white' : 'text-gray-600 hover:text-gray-800'"
      >
        <span class="text-lg">←</span>
        <span>Geri</span>
      </button>
      <h1 class="text-2xl font-bold">Şişe Çevirmece</h1>
    </div>

    <!-- Setup Phase -->
    <div v-if="gameState === 'setup'" class="space-y-8">
      <!-- Player Count Selection -->
      <div class="space-y-4">
        <label class="block text-lg font-medium text-center mb-4">Oyuncu Sayısı</label>
        <div class="flex gap-2">
          <button
            v-for="count in [2,3,4,5,6]"
            :key="`count-${count}`"
            @click="selectPlayerCount(count)"
            :disabled="playerCount !== null"
            :class="[
              'flex-1 px-4 py-3 rounded-xl font-medium transition-all duration-50',
              playerCount === count
                ? 'bg-blue-500 text-white'
                : playerCount !== null
                  ? 'opacity-50 cursor-not-allowed'
                  : isDarkMode
                    ? 'bg-gray-700 text-gray-300 hover:bg-gray-600'
                    : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
            ]"
          >
            {{ count }}
          </button>
        </div>
      </div>

      <!-- Name Inputs -->
      <div v-if="playerCount" class="space-y-6">
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div v-for="(_, index) in playerNames" :key="`input-${index}`" class="space-y-2">
            <label :for="`player-${index}`" class="block text-sm font-medium">
              {{ index + 1 }}. Oyuncu
            </label>
            <input
              :id="`player-${index}`"
              v-model="playerNames[index]"
              type="text"
              :placeholder="'İsim girin...'"
              class="w-full px-4 py-3 rounded-xl border-2 transition-all duration-50"
              :class="isDarkMode 
                ? 'bg-gray-700 border-gray-600 text-white placeholder-gray-400'
                : 'bg-white border-gray-200 text-gray-900'"
              @keyup.enter="index < playerNames.length - 1 ? $refs[`input-${index + 1}`]?.[0]?.focus() : null"
              :ref="`input-${index}`"
            >
          </div>
        </div>

        <!-- Start Game Button -->
        <button
          @click="startGame"
          :disabled="!areAllNamesFilled"
          class="w-full px-6 py-4 rounded-xl text-white font-semibold transition-all duration-50"
          :class="areAllNamesFilled
            ? 'bg-blue-500 hover:bg-blue-600'
            : 'bg-gray-400 cursor-not-allowed'"
        >
          Oyunu Başlat
        </button>
      </div>
    </div>

    <!-- Game Phase -->
    <div v-else class="space-y-8">
      <!-- Bottle Spinning Area -->
      <div class="relative aspect-square max-w-md mx-auto">
        <!-- Player Circle with Sections -->
        <div class="absolute inset-0 rounded-full overflow-hidden">
          <!-- Colored Sections -->
          <div
            v-for="(player, index) in players"
            :key="`section-${index}`"
            class="absolute w-full h-full origin-center transition-opacity duration-200"
            :style="{
              transform: `rotate(${index * (360 / players.length)}deg)`,
              clipPath: `polygon(0 0, 50% 50%, ${50 + 50 * Math.cos((360 / players.length) * Math.PI / 180)}% ${50 + 50 * Math.sin((360 / players.length) * Math.PI / 180)}%)`
            }"
            :class="[
              sectionColors[index % sectionColors.length],
              selectedPlayer === index ? 'opacity-100' : 'opacity-70'
            ]"
          />
        </div>

        <!-- Player Names -->
        <div
          v-for="(player, index) in players"
          :key="`name-${index}`"
          class="absolute left-1/2 -translate-x-1/2 origin-bottom whitespace-nowrap"
          :style="{
            transform: `rotate(${index * (360 / players.length)}deg) translateY(-${players.length > 4 ? '20px' : '30px'})`,
          }"
        >
          <div 
            class="px-3 py-1 rounded-full text-sm font-medium transform -rotate-[attr(data-rotation)]"
            :style="{
              transform: `rotate(-${index * (360 / players.length)}deg)`,
              backgroundColor: selectedPlayer === index ? 'rgba(255, 255, 255, 0.9)' : 'rgba(255, 255, 255, 0.7)'
            }"
          >
            {{ player }}
          </div>
        </div>

        <!-- Bottle -->
        <div
          class="absolute top-1/2 left-1/2 transition-transform duration-[3000ms] ease-out"
          :style="`transform: translate(-50%, -50%) rotate(${rotationDegree}deg)`"
        >
          <div class="relative">
            <!-- Bottle Body -->
            <div class="absolute top-0 left-1/2 -translate-x-1/2 w-1 h-32 bg-white rounded-full shadow-lg">
              <!-- Bottle Head -->
              <div class="absolute -top-2 left-1/2 -translate-x-1/2 w-3 h-3 bg-white rounded-full shadow-md"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Game Controls -->
      <div class="space-y-4">
        <button
          v-if="!selectedChoice && !isSpinning"
          @click="spinBottle"
          class="w-full px-6 py-4 rounded-xl text-white font-semibold transition-all duration-50"
          :class="isSpinning ? 'bg-gray-500' : 'bg-blue-500 hover:bg-blue-600'"
        >
          Şişeyi Çevir
        </button>

        <!-- Truth or Dare Selection -->
        <div v-if="selectedPlayer !== null && !selectedChoice && !isSpinning" 
             class="flex gap-2"
        >
          <button
            @click="makeChoice('truth')"
            class="flex-1 px-6 py-4 rounded-xl text-white font-semibold bg-green-500 hover:bg-green-600 transition-all duration-50"
          >
            Doğruluk
          </button>
          <button
            @click="makeChoice('dare')"
            class="flex-1 px-6 py-4 rounded-xl text-white font-semibold bg-red-500 hover:bg-red-600 transition-all duration-50"
          >
            Cesaret
          </button>
        </div>

        <!-- Question/Dare Display -->
        <div v-if="currentQuestion"
             class="p-6 rounded-xl text-center text-lg font-medium"
             :class="selectedChoice === 'truth' 
               ? (isDarkMode ? 'bg-green-900/50 text-green-200' : 'bg-green-100 text-green-700')
               : (isDarkMode ? 'bg-red-900/50 text-red-200' : 'bg-red-100 text-red-700')"
        >
          {{ currentQuestion }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.ease-out {
  transition-timing-function: cubic-bezier(0.33, 1, 0.68, 1);
}

/* Add smooth transitions for disabled states */
button:disabled {
  transition: all 0.3s ease;
}

/* Improve input focus states */
input:focus {
  outline: none;
  border-color: rgb(59, 130, 246);
  box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.1);
}

/* Add smooth section transitions */
.section-transition {
  transition: opacity 0.3s ease;
}

/* Improve bottle appearance */
.bottle-shadow {
  filter: drop-shadow(0 0 8px rgba(0, 0, 0, 0.2));
}
</style> 