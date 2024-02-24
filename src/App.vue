<script setup>
import {onMounted, ref} from "vue";
//import { RouterLink, RouterView } from 'vue-router'

const username = ref(''),
    password = ref(''),
    posts = ref([]),
    users = ref([]),
    currentUser = ref({
      login: "",
      role: "",
      isAuthorized: false
    }),
    postToEdit = ref({}),
    isEditModalOpen = ref(false);

const login = () => {
  const userIndex = users.value.findIndex(user => {
    return user.login === username.value && user.password.toString() === password.value;
  })

  if (userIndex > -1) {
    currentUser.value = {
      id: Number(users.value[userIndex].id),
      login: users.value[userIndex].login,
      role: users.value[userIndex].role,
      isAuthorized: true
    }
  }
}

const logout = () => {
  currentUser.value = {
    id: "",
    login: "",
    role: "",
    isAuthorized: false
  }
}

const addPost = (post) => {
  fetch('http://localhost:3000/posts/1', {
    method: "POST",
    body: JSON.stringify(post)
  })
      .then(resp => resp.json())
      .then(resp => {
        //updatePosts();
      })
}

const deletePost = (id) => {
  fetch(`http://localhost:3000/posts/${id}`, {
    method: "DELETE",
  })
      .then(resp => resp.json())
      .then(() => {
        const postIndex = posts.value.findIndex(post => post.id === id);
        posts.value.splice(postIndex, 1);
      })
}

const updatePosts = () => {
  fetch('http://localhost:3000/posts')
      .then(resp => resp.json())
      .then(resp => {
        posts.value = resp
      })
}

const updateUsers = () => {
  fetch('http://localhost:3000/users')
      .then(resp => resp.json())
      .then(resp => {
        users.value = resp
      })
}

const clap = (id) => {
  const post = posts.value.find(post => post.id === id);
  const clapsData = {
    "claps": ++post.claps
  }

  fetch('http://localhost:3000/posts/' + id, {
    method: "PATCH",
    body: JSON.stringify(clapsData)
  })
}

const openEditModal = (id) => {
  isEditModalOpen.value = true;

  if (id) {
    const post = posts.value.find(post => post.id === id);
    postToEdit.value = Object.assign(postToEdit.value, post);
  }
}
const cancelEdit = () => {
  isEditModalOpen.value = false;
  postToEdit.value = {};
}

const savePost = (isCreate) => {
  if (isCreate) {

  } else {

  }

  cancelEdit();
}

onMounted(() => {
  updatePosts();
  updateUsers();
})
</script>

<template>
  <header>
    <h1>Auth app</h1>
  </header>

  <main>
    <div class="container">
      <div class="user" v-if="currentUser.isAuthorized">
        <p>login: {{ currentUser.login }}</p>
        <p>role: {{ currentUser.role }}</p>
        <p>
          <button @click="logout">logout</button>
        </p>
      </div>

      <form class="auth" v-if="!currentUser.isAuthorized" @submit.prevent="login()">
        <div class="auth__wrapper">
          <label class="auth__label">
            <span>Имя пользователя</span>
            <input type="text" v-model="username">
          </label>

          <label class="auth__label">
            <span>Пароль</span>
            <input type="text" v-model="password">
          </label>

          <button class="auth__submit">
            Войти
          </button>
        </div>
      </form>

      <div class="posts">
        <ul class="posts__list">
          <li class="posts__item" v-for="post in posts"  v-bind:key="post.id">
            <div class="posts__title">{{ post.title }}</div>
            <div class="posts__text">{{ post.description }}</div>
            <div class="posts__control" v-if="currentUser.isAuthorized">
              <button @click="openEditModal(post.id)" v-if="currentUser.role === 'writer' && post.userId === currentUser.id">edit</button>
              <button @click="deletePost(post.id)" v-if="currentUser.role === 'writer' && post.userId === currentUser.id">delete</button>
              <button @click="clap(post.id)" v-if="post.userId !== currentUser.id">clap - {{ post.claps }}</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </main>

  <div class="modal" v-if="isEditModalOpen">
    <div class="modal__fade"></div>

    <div class="modal__box">
      <input type="text" placeholder="Title" v-model="postToEdit.title">
      <textarea name="" id="" cols="30" rows="10" placeholder="Text" v-model="postToEdit.description"></textarea>

      <div class="modal__buttons">
        <button @click="savePost(true)">save</button>
        <button @click="cancelEdit()">cancel</button>
      </div>
    </div>
  </div>
</template>

<!--Как лучше перерисовывать список после добавления/удаления поста-->
<!--Как правильно применить debounce для запросов-->