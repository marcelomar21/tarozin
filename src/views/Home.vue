<template>
  <div>
    <div v-if="currentUser" class="user-info">
      <p>Créditos: {{ currentUser.credits }}</p>
      <button @click="addCredits">Adicionar Créditos</button>
      <router-link to="/history" class="history-link">Ver Histórico</router-link>
    </div>
    <div class="game-buttons">
      <button @click="playThreeCards" :disabled="!canPlay">3 Cartas</button>
      <button @click="playRoyalTable" :disabled="!canPlay">Mesa Real</button>
    </div>
    <game-area 
      :cards="drawnCards" 
      :gameType="currentGame" 
      @interpretation-ready="saveInterpretation"
    />
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import GameArea from '../components/GameArea.vue'
import { tarotDeck, shuffleDeck, drawCards } from '../utils/tarotUtils'

export default {
  components: {
    GameArea
  },
  props: ['currentUser'],
  emits: ['update-user'],
  setup(props, { emit }) {
    const drawnCards = ref([])
    const currentGame = ref('')

    const canPlay = computed(() => props.currentUser && props.currentUser.credits > 0)

    const playThreeCards = () => {
      if (canPlay.value) {
        const shuffledDeck = shuffleDeck([...tarotDeck])
        drawnCards.value = drawCards(shuffledDeck, 3)
        currentGame.value = 'threeCards'
        updateCredits()
      } else {
        alert('Você não tem créditos suficientes para jogar.')
      }
    }

    const playRoyalTable = () => {
      if (canPlay.value) {
        const shuffledDeck = shuffleDeck([...tarotDeck])
        drawnCards.value = drawCards(shuffledDeck, 32)
        currentGame.value = 'royalTable'
        updateCredits()
      } else {
        alert('Você não tem créditos suficientes para jogar.')
      }
    }

    const updateCredits = () => {
      if (props.currentUser) {
        const updatedUser = { ...props.currentUser, credits: props.currentUser.credits - 1 }
        emit('update-user', updatedUser)
      }
    }

    const addCredits = () => {
      if (props.currentUser) {
        const updatedUser = { ...props.currentUser, credits: props.currentUser.credits + 10 }
        emit('update-user', updatedUser)
      }
    }

    const saveInterpretation = (interpretation) => {
      if (props.currentUser) {
        const newGame = {
          gameType: currentGame.value,
          date: new Date().toISOString(),
          cards: drawnCards.value,
          interpretation: interpretation
        }
        const updatedUser = {
          ...props.currentUser,
          gameHistory: [...(props.currentUser.gameHistory || []), newGame]
        }
        emit('update-user', updatedUser)
      }
    }

    return {
      drawnCards,
      currentGame,
      canPlay,
      playThreeCards,
      playRoyalTable,
      addCredits,
      saveInterpretation
    }
  }
}
</script>

<style scoped>
.user-info {
  margin-bottom: 20px;
}

.game-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

.game-buttons button {
  padding: 10px 20px;
  font-size: 16px;
}

.history-link {
  margin-left: 10px;
  text-decoration: none;
  color: #9370db;
}

.history-link:hover {
  text-decoration: underline;
}
</style>