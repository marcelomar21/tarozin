<template>
  <div class="game-area">
    <h2>{{ gameTitle }}</h2>
    <div :class="['cards-container', gameType]">
      <div v-for="(card, index) in cards" :key="index" class="card">
        <div class="card-inner" :class="{ flipped: flippedCards[index] }">
          <div class="card-front"></div>
          <div class="card-back">
            <img :src="card.image" :alt="card.name">
            <p>{{ card.name }}</p>
          </div>
        </div>
      </div>
    </div>
    <div v-if="gameType === 'royalTable'" class="royal-table-info">
      <p>A Mesa Real é composta por 32 cartas, representando diferentes aspectos da vida.</p>
    </div>
    <button @click="revealCards" :disabled="allCardsRevealed">Revelar Cartas</button>
    <div v-if="allCardsRevealed" class="interpretation">
      <h3>Interpretação do Jogo</h3>
      <p>{{ gameInterpretation }}</p>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'

export default {
  props: ['cards', 'gameType'],
  emits: ['interpretation-ready'],
  setup(props, { emit }) {
    const flippedCards = ref({})
    const revealInterval = ref(null)
    const currentRevealIndex = ref(0)
    const gameInterpretation = ref('')

    const allCardsRevealed = computed(() => {
      return Object.keys(flippedCards.value).length === props.cards.length &&
             Object.values(flippedCards.value).every(flipped => flipped)
    })

    const gameTitle = computed(() => {
      return props.gameType === 'threeCards' ? 'Jogo das 3 Cartas' : 'Mesa Real'
    })

    const revealCards = () => {
      if (revealInterval.value) return

      revealInterval.value = setInterval(() => {
        if (currentRevealIndex.value < props.cards.length) {
          flippedCards.value[currentRevealIndex.value] = true
          currentRevealIndex.value++
        } else {
          clearInterval(revealInterval.value)
          revealInterval.value = null
          generateInterpretation()
        }
      }, 500)
    }

    const generateInterpretation = () => {
      const cardNames = props.cards.map(card => card.name).join(', ')
      gameInterpretation.value = `As cartas reveladas (${cardNames}) sugerem um período de ${getRandomInterpretation()}. 
      É importante lembrar que o Tarot é uma ferramenta de reflexão e as interpretações podem variar de acordo com o contexto pessoal.`
      emit('interpretation-ready', gameInterpretation.value)
    }

    const getRandomInterpretation = () => {
      const interpretations = [
        "transformação e crescimento pessoal",
        "desafios que levarão a novas oportunidades",
        "reflexão sobre suas escolhas e caminhos",
        "harmonia e equilíbrio em suas relações",
        "necessidade de ação e iniciativa",
        "introspecção e autoconhecimento",
        "mudanças positivas em sua vida profissional",
        "fortalecimento de laços afetivos",
        "superação de obstáculos e realização pessoal"
      ]
      return interpretations[Math.floor(Math.random() * interpretations.length)]
    }

    watch(() => props.cards, () => {
      flippedCards.value = {}
      currentRevealIndex.value = 0
      gameInterpretation.value = ''
      if (revealInterval.value) {
        clearInterval(revealInterval.value)
        revealInterval.value = null
      }
    }, { deep: true })

    return {
      flippedCards,
      gameTitle,
      revealCards,
      allCardsRevealed,
      gameInterpretation
    }
  }
}
</script>

<style scoped>
.game-area {
  text-align: center;
  padding: 20px;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

.cards-container.threeCards {
  max-width: 600px;
  margin: 0 auto;
}

.cards-container.royalTable {
  max-width: 1000px;
  margin: 0 auto;
}

.card {
  width: 100px;
  height: 175px;
  perspective: 1000px;
  cursor: pointer;
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}

.card-inner.flipped {
  transform: rotateY(180deg);
}

.card-front, .card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.card-front {
  background-image: url('https://i.pinimg.com/originals/96/fe/b2/96feb2df0b9d41b2e1d9b6d2b1e2c7e1.jpg');
  background-size: cover;
  background-position: center;
}

.card-back {
  background-color: white;
  transform: rotateY(180deg);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  padding: 5px;
  box-sizing: border-box;
}

.card-back img {
  max-width: 80%;
  max-height: 60%;
  object-fit: contain;
  margin-bottom: 5px;
}

.royal-table-info {
  margin-top: 20px;
  font-style: italic;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #9370db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:disabled {
  background-color: #d8bfd8;
  cursor: not-allowed;
}

.interpretation {
  margin-top: 20px;
  padding: 15px;
  background-color: #f9f9f9;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.interpretation h3 {
  margin-bottom: 10px;
  color: #333;
}

.interpretation p {
  line-height: 1.6;
  color: #555;
}

@media (max-width: 768px) {
  .card {
    width: 80px;
    height: 140px;
  }

  .cards-container.royalTable {
    max-width: 100%;
  }
}
</style>