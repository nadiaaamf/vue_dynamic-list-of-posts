<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  post: Object,
})

const emit = defineEmits([
  'save',
  'delete',
  'close',
])

const title = ref('')
const body = ref('')

watch(
  () => props.post,
  newPost => {
    title.value = newPost?.title || ''
    body.value = newPost?.body || ''
  },
  { immediate: true }
)

function handleSave() {
  const newPost = {
    id: props.post?.id || Date.now(),
    title: title.value,
    body: body.value,
  }

  emit('save', newPost)
}

function handleDelete() {
  if (props.post) {
    emit('delete', props.post.id)
  }
}

function handleClose() {
  emit('close')
}
</script>

<template>
  <div class="box mt-4">
    <h2 class="title is-4">
      {{ post ? 'Edit Post' : 'New Post' }}
    </h2>

    <div class="field">
      <label class="label">
        Title
      </label>

      <input
        v-model="title"
        class="input"
        placeholder="Post title"
      >
    </div>

    <div class="field">
      <label class="label">
        Body
      </label>

      <textarea
        v-model="body"
        class="textarea"
        placeholder="Post content"
      />
    </div>

    <div class="buttons mt-4">
      <button
        class="button is-success"
        @click="handleSave"
      >
        Save
      </button>

      <button
        v-if="post"
        class="button is-danger"
        @click="handleDelete"
      >
        Delete
      </button>

      <button
        class="button"
        @click="handleClose"
      >
        Cancel
      </button>
    </div>
  </div>
</template>