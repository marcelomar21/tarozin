<template>
  <div class="game-history">
    <h2>Histórico de Jogos</h2>
    <router-link to="/" class="back-button">Voltar</router-link>
    <div v-if="!currentUser">
      <p>Faça login para ver seu histórico de jogos.</p>
    </div>
    <div v-else-if="currentUser.gameHistory && currentUser.gameHistory.length === 0" class="no-history">
      Você ainda não jogou nenhum jogo.
    </div>
    <div v-else class="history-list">
      <div v-for="(game, index) in currentUser.gameHistory" :key="index" class="history-item">
        <h3>{{ game.gameType }} - {{ formatDate(game.date) }}</h3>
        <div class="cards-container">
          <div v-for="(card, cardIndex) in game.cards" :key="cardIndex" class="card">
            <img :src="card.image" :alt="card.name" />
            <p>{{ card.name }}</p>
          </div>
        </div>
        <div v-if="game.interpretation" class="interpretation">
          <h4>Interpretação:</h4>
          <p>{{ game.interpretation }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['currentUser'],
  methods: {
    formatDate(dateString) {
      const options = { year: 'numeric', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit' };
      return new Date(dateString).toLocaleDateString('pt-BR', options);
    }
  }
}
</script>

<style scoped>
.game-history {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.back-button {
  display: inline-block;
  margin-bottom: 20px;
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  text-decoration: none;
  border-radius: 5px;
}

.back-button:hover {
  background-color: #45a049;
}

.no-history {
  text-align: center;
  margin-top: 20px;
  font-style: italic;
}

.history-item {
  background-color: #f9f9f9;
  border-radius: 10px;
  padding: 20px;
  margin-bottom: 30px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

.card {
  width: 80px;
  text-align: center;
}

.card img {
  width: 100%;
  height: auto;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.card p {
  font-size: 12px;
  margin-top: 5px;
}

.interpretation {
  background-color: #f0f0f0;
  border-radius: 5px;
  padding: 15px;
  margin-top: 20px;
}

.interpretation h4 {
  margin-bottom: 10px;
  color: #333;
}

.interpretation p {
  font-style: italic;
  color: #555;
  line-height: 1.6;
}

@media (max-width: 768px) {
  .cards-container {
    justify-content: flex-start;
  }

  .card {
    width: 60px;
  }
}
</style>