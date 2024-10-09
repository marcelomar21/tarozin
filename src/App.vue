<template>
  <div class="app-container">
    <nav-bar 
      @show-login="showLoginForm" 
      @show-register="showRegisterForm" 
      @logout="logout" 
      :user="currentUser" 
    />
    <header>
      <h1>Tarozin</h1>
    </header>
    <div class="content">
      <login-form v-if="showLogin" @login="login" />
      <register-form v-if="showRegister" @register="register" />
      <router-view 
        :currentUser="currentUser" 
        @update-user="updateUser"
      ></router-view>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'
import NavBar from './components/NavBar.vue'
import LoginForm from './components/LoginForm.vue'
import RegisterForm from './components/RegisterForm.vue'
import { useRouter } from 'vue-router'

export default {
  components: {
    NavBar,
    LoginForm,
    RegisterForm
  },
  setup() {
    const currentUser = ref(null)
    const showLogin = ref(false)
    const showRegister = ref(false)
    const router = useRouter()

    const showLoginForm = () => {
      showLogin.value = true
      showRegister.value = false
    }

    const showRegisterForm = () => {
      showRegister.value = true
      showLogin.value = false
    }

    const login = (email, password) => {
      const users = JSON.parse(localStorage.getItem('users') || '[]')
      const user = users.find(u => u.email === email && u.password === password)
      if (user) {
        currentUser.value = user
        localStorage.setItem('currentUser', JSON.stringify(user))
        showLogin.value = false
        router.push('/')
      } else {
        alert('Email ou senha incorretos.')
      }
    }

    const register = (user) => {
      const users = JSON.parse(localStorage.getItem('users') || '[]')
      if (users.some(u => u.email === user.email)) {
        alert('Este email já está cadastrado.')
        return
      }
      user.gameHistory = []
      users.push(user)
      localStorage.setItem('users', JSON.stringify(users))
      currentUser.value = user
      localStorage.setItem('currentUser', JSON.stringify(user))
      showRegister.value = false
      router.push('/')
    }

    const logout = () => {
      currentUser.value = null
      localStorage.removeItem('currentUser')
      router.push('/')
    }

    const updateUser = (updatedUser) => {
      currentUser.value = updatedUser
      const users = JSON.parse(localStorage.getItem('users') || '[]')
      const updatedUsers = users.map(u => u.email === updatedUser.email ? updatedUser : u)
      localStorage.setItem('users', JSON.stringify(updatedUsers))
      localStorage.setItem('currentUser', JSON.stringify(updatedUser))
    }

    watch(() => currentUser.value, (newUser) => {
      if (newUser) {
        showLogin.value = false
        showRegister.value = false
      }
    })

    // Carregar usuário do localStorage ao iniciar
    const storedUser = localStorage.getItem('currentUser')
    if (storedUser) {
      currentUser.value = JSON.parse(storedUser)
    }

    return {
      currentUser,
      showLogin,
      showRegister,
      showLoginForm,
      showRegisterForm,
      login,
      register,
      logout,
      updateUser
    }
  }
}
</script>

<style>
.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 20px;
}

h1 {
  color: #9370db;
}

.content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}
</style>