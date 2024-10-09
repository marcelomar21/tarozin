<template>
  <nav class="nav-bar">
    <div class="container">
      <template v-if="!user">
        <button @click="$emit('show-login')">Login</button>
        <button @click="$emit('show-register')">Cadastro</button>
      </template>
      <template v-else>
        <span class="welcome-message">Bem-vindo, {{ user.name }}!</span>
        <router-link to="/" class="nav-link">Início</router-link>
        <router-link to="/history" class="nav-link">Histórico</router-link>
        <button @click="$emit('logout')">Sair</button>
      </template>
    </div>
  </nav>
</template>

<script>
export default {
  props: ['user']
}
</script>

<style scoped>
.nav-bar {
  background-color: #9370db; /* Roxo médio para a barra de navegação */
  padding: 10px 0;
}

.container {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

button, .nav-link {
  margin-left: 10px;
  padding: 5px 10px;
  background-color: #8a2be2; /* Roxo mais escuro para botões */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 14px;
}

button:hover, .nav-link:hover {
  background-color: #6a0dad; /* Roxo ainda mais escuro no hover */
}

.welcome-message {
  margin-right: auto;
  color: white;
}
</style>