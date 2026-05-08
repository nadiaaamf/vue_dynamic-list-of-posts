<script setup>
const emit = defineEmits(['logout'])

function handleLogout() {
  emit('logout')
}
</script>

<template>
  <nav
    class="navbar is-link px-4"
    role="navigation"
  >
    <div class="navbar-brand">
      <h1 class="navbar-item has-text-weight-bold is-size-4">
        Dynamic Posts
      </h1>
    </div>

    <div class="navbar-end">
      <div class="navbar-item">
        <button
          class="button is-light"
          @click="handleLogout"
        >
          Logout
        </button>
      </div>
    </div>
  </nav>
</template>