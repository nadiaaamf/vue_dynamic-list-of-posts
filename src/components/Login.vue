<script setup>
import { ref } from "vue";

const emit = defineEmits(["login"]);

const email = ref("");
const error = ref(false);

async function handleLogin() {
  if (!email.value) {
    error.value = true

    return
  }

  error.value = false

  try {
    const response = await fetch(
      'https://mate-academy.github.io/fe-students-api/api/users'
    )

    const users = await response.json()

    const user = users.find(
      currentUser => currentUser.email === email.value
    )

    if (!user) {
      error.value = true

      return
    }

    emit('login', user.id)
  } catch (err) {
    console.error(err)

    error.value = true
  }
}
</script>

<template>
  <section class="hero is-fullheight has-background-light">
    <div class="hero-body">
      <div class="container">
        <div class="columns is-centered">
          <div class="column is-4-desktop is-5-tablet">
            <div class="box p-6">
              <h1 class="title is-1 mb-4">Get your userId</h1>

              <div class="field">
                <label class="label"> Email </label>

                <div class="control has-icons-left">
                  <input
                    v-model="email"
                    class="input"
                    type="email"
                    placeholder="Enter your email"
                    @input="error = false"
                  />

                  <span class="icon is-small is-left">
                    <i class="fas fa-envelope"></i>
                  </span>
                </div>

                <p v-if="error" class="help is-danger">Email is required</p>
              </div>

              <button
                class="button is-primary is-medium mt-4"
                @click="handleLogin"
              >
                Login
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero {
  background: #f5f5f5;
}

.box {
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.08);
}

.title {
  color: #2d3748;
}

.input {
  height: 52px;
}

.button {
  min-width: 120px;
  background-color: #479b97;
  padding-top: 10px;
  padding-bottom: 10px;
  border-radius: 8px;
  border: none;
  color: #f5f5f5;
  margin-top: 10px;
}
</style>
