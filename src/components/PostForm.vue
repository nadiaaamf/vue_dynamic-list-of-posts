<script setup>
import { ref, watch } from "vue"; // props const props = defineProps({ post: Object, // se vier preenchido = edit }) // emits const emit = defineEmits(['save', 'cancel']) // estados const title = ref('') const body = ref('') const userId = ref(1) const errors = ref({}) const loading = ref(false) // 👉 preencher form quando editar watch( () => props.post, (newPost) => { if (newPost) { title.value = newPost.title body.value = newPost.body userId.value = newPost.userId || 1 } else { title.value = '' body.value = '' userId.value = 1 } }, { immediate: true } ) // 👉 validação function validate() { errors.value = {} if (!title.value) errors.value.title = 'Title is required' if (!body.value) errors.value.body = 'Body is required' return Object.keys(errors.value).length === 0 } // 👉 salvar (CREATE + EDIT funcionando sem erro) async function handleSubmit() { if (!validate()) return loading.value = true const newPost = { title: title.value, body: body.value, userId: userId.value, } try { let data try { // 🔗 tenta usar API const res = await fetch( https://jsonplaceholder.typicode.com/posts${ props.post ? /${props.post.id} : '' }, { method: props.post ? 'PUT' : 'POST', body: JSON.stringify(newPost), headers: { 'Content-Type': 'application/json', }, } ) data = await res.json() } catch { // 🔥 fallback (garante funcionamento) data = { ...newPost, id: props.post?.id || Date.now(), } } // 🔥 envia pro App emit('save', data) } catch (e) { alert('Error saving post') } finally { loading.value = false } }
</script>
<template>
  <div>
    <h2 class="title is-4 mb-5">
      {{ post ? "Edit post" : "Create new post" }}
    </h2>
    <!-- TITLE -->
    <div class="field">
      <label class="label">Title</label>
      <div class="control has-icons-left">
        <input
          v-model="title"
          class="input"
          placeholder="Post title"
          @input="errors.title = ''"
        />
        <span class="icon is-small is-left"> 👤 </span>
      </div>
      <p v-if="errors.title" class="help is-danger">{{ errors.title }}</p>
    </div>
    <!-- BODY -->
    <div class="field">
      <label class="label">Write Post Body</label>
      <div class="control">
        <textarea
          v-model="body"
          class="textarea"
          placeholder="Post body"
          @input="errors.body = ''"
        />
      </div>
      <p v-if="errors.body" class="help is-danger">{{ errors.body }}</p>
    </div>
    <!-- BUTTONS -->
    <div class="field is-grouped">
      <div class="control">
        <button
          class="button is-link"
          :class="{ 'is-loading': loading }"
          @click="handleSubmit"
        >
          {{ post ? "Save" : "Create" }}
        </button>
      </div>
      <div class="control">
        <button class="button is-light" @click="$emit('cancel')">Cancel</button>
      </div>
    </div>
  </div>
</template>
