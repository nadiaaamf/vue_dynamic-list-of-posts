<template>
  <Login
    v-if="!userId"
    @login="handleLogin"
  />

  <div v-else>
    <Header />

    <div class="container mt-4 has-text-right">
      <button
        class="button is-danger"
        @click="logout"
      >
        Logout
      </button>
    </div>

    <div class="container mt-5">
      <PostLists
        :posts="posts"
        :loading="loading"
        :error="error"
        :selected-post="selectedPost"
        @open-post="openPost"
        @create-post="createPost"
        @close-post="closePost"
      />

      <Sidebar
        v-if="isSidebarOpen"
        :post="selectedPost"
        @save="savePost"
        @delete="deletePost"
        @close="closePost"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

import Header from './components/Header.vue'
import Login from './components/Login.vue'
import PostLists from './components/PostLists.vue'
import Sidebar from './components/Sidebar.vue'

const userId = ref(null)

const posts = ref([])
const selectedPost = ref(null)

const isSidebarOpen = ref(false)

const loading = ref(false)
const error = ref(false)

async function handleLogin(id) {
  userId.value = id

  await loadPosts()
}

async function loadPosts() {
  if (!userId.value) {
    return
  }

  loading.value = true
  error.value = false

  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts?userId=${userId.value}`
    )

    posts.value = await response.json()
  } catch (err) {
    error.value = true

    console.error(err)
  } finally {
    loading.value = false
  }
}

function openPost(post) {
  selectedPost.value = post
  isSidebarOpen.value = true
}

function createPost() {
  selectedPost.value = null
  isSidebarOpen.value = true
}

function closePost() {
  selectedPost.value = null
  isSidebarOpen.value = false
}

function savePost(post) {
  const postIndex = posts.value.findIndex(
    currentPost => currentPost.id === post.id
  )

  if (postIndex !== -1) {
    posts.value[postIndex] = {
      ...post,
    }

    posts.value = [...posts.value]
  } else {
    posts.value = [
      post,
      ...posts.value,
    ]
  }

  selectedPost.value = post

  isSidebarOpen.value = false
}

function deletePost(id) {
  posts.value = posts.value.filter(
    post => post.id !== id
  )

  closePost()
}

function logout() {
  userId.value = null

  posts.value = []
  selectedPost.value = null

  isSidebarOpen.value = false
}
</script>