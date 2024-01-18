<script setup>
//import { RouterLink, RouterView } from 'vue-router'

fetch('http://localhost:3000/')
.then(res => res.json())
.then(res => {
  console.log(res)
})
</script>

<template>
  <header>
    <h1>Auth app</h1>
  </header>

  <main>
    <div class="container">
      <form class="auth">
        <div class="auth__wrapper">
          <label class="auth__label">
            <span>Имя пользователя</span>
            <input type="text">
          </label>

          <label class="auth__label">
            <span>Пароль</span>
            <input type="text">
          </label>

          <button class="auth__submit">
            Войти
          </button>
        </div>
      </form>
    </div>
  </main>
</template>
