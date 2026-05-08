<script setup>
import { ref, watch } from 'vue'
import Loader from './Loader.vue'       // ✅
import CommentForm from './CommentForm.vue'

const props = defineProps({ post: Object })
const emit = defineEmits(['save', 'delete', 'close'])

const title = ref('')
const body = ref('')
const comments = ref([])
const commentsLoading = ref(false)
const commentsError = ref(false)   // ✅

watch(
  () => props.post,
  async newPost => {
    title.value = newPost?.title || ''
    body.value = newPost?.body || ''
    comments.value = []

    if (newPost?.id) {
      await loadComments(newPost.id)
    }
  },
  { immediate: true }
)

async function loadComments(postId) {
  commentsLoading.value = true
  commentsError.value = false

  try {
    const response = await fetch(
      'https://mate-academy.github.io/fe-students-api/api/comments'  // ✅ URL corrigida
    )
    const data = await response.json()
    comments.value = data.filter(comment => comment.postId === postId)
  } catch (err) {
    commentsError.value = true   // ✅
    console.error(err)
  } finally {
    commentsLoading.value = false
  }
}

function addComment(commentBody) {
  comments.value.unshift({
    id: Date.now(),
    postId: props.post.id,
    name: 'New Comment',
    body: commentBody,
  })
}

function deleteComment(commentId) {
  comments.value = comments.value.filter(c => c.id !== commentId)
}

function handleSave() {
  emit('save', {
    id: props.post?.id || Date.now(),
    title: title.value,
    body: body.value,
  })
}

function handleDelete() {
  if (props.post) emit('delete', props.post.id)
}
</script>

<template>
  <div class="sidebar box mt-4" :class="{ 'Sidebar--open': post }">
    <h2 class="title is-4">{{ post ? 'Edit Post' : 'New Post' }}</h2>

    <div class="field">
      <label class="label">Title</label>
      <input v-model="title" class="input" placeholder="Post title" />
    </div>

    <div class="field">
      <label class="label">Body</label>
      <textarea v-model="body" class="textarea" placeholder="Post content" />
    </div>

    <div class="buttons mt-4">
      <button class="button is-success" @click="handleSave">Save</button>
      <button v-if="post" class="button is-danger" @click="handleDelete">Delete</button>
      <button class="button" @click="emit('close')">Cancel</button>
    </div>

    <hr />

    <div class="mt-5">
      <h3 class="title is-5">Comments</h3>

      <!-- ✅ CommentForm com botão "Write a comment" -->
      <CommentForm v-if="post" @add-comment="addComment" />

      <!-- ✅ Loader -->
      <Loader v-if="commentsLoading" />

      <!-- ✅ Erro -->
      <div v-else-if="commentsError" class="notification is-danger">
        Failed to load comments
      </div>

      <!-- ✅ Array vazio -->
      <div v-else-if="!comments.length" class="has-text-centered p-4">
        No comments yet
      </div>

      <!-- ✅ Lista de comentários com delete -->
      <div v-for="comment in comments" :key="comment.id" class="box">
        <div class="is-flex is-justify-content-space-between mb-2">
          <p class="has-text-weight-bold">{{ comment.name }}</p>
          <button
            class="button is-danger is-small"
            @click="deleteComment(comment.id)"
          >
            Delete
          </button>
        </div>
        <p>{{ comment.body }}</p>
      </div>
    </div>
  </div>
</template>