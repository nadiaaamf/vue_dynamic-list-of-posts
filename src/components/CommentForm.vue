<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-comment'])

const body = ref('')
const isOpen = ref(false)  // ✅ controla visibilidade do form

function handleSubmit() {
  if (!body.value.trim()) return

  emit('add-comment', body.value)

  body.value = ''
  isOpen.value = false
}

function handleCancel() {
  body.value = ''
  isOpen.value = false
}
</script>

<template>
  <div class="mb-4">
    <!-- ✅ Botão "Write a comment" -->
    <button
      v-if="!isOpen"
      class="button is-link is-light mb-3"
      @click="isOpen = true"
    >
      Write a comment
    </button>

    <div v-else>
      <div class="field">
        <label class="label">Comment</label>
        <div class="control">
          <textarea
            v-model="body"
            class="textarea"
            placeholder="Write your comment here..."
          />
        </div>
      </div>

      <div class="buttons">
        <button class="button is-success is-small" @click="handleSubmit">
          Add Comment
        </button>
        <button class="button is-light is-small" @click="handleCancel">
          Cancel
        </button>
      </div>
    </div>
  </div>
</template>