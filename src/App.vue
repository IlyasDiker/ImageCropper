<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header class="flex flex-col bg-slate-900 text-slate-200">
    <nav class="container mx-auto px-4 p-4 flex flex-row justify-between items-center">
      <ul class="flex flex-row gap-5">
        <li class="items-center text-2xl font-bold">
          <router-link :to="{name:'home'}">
            ImageCropper
          </router-link>
        </li>
      </ul>
      <ul class="nav-items flex flex-row gap-5">
        <li class="router-link">
          <RouterLink to="/">Home</RouterLink>
        </li>
        <li class="router-link">
          <RouterLink to="/about">About</RouterLink>
        </li>
      </ul>
    </nav>
  </header>

  <RouterView />
</template>

<style lang="scss" scoped>
.nav-items{
  .router-link-active{
    @apply font-bold bg-slate-800;
  }
  .router-link{
    @apply flex justify-center align-middle items-center min-w-[100px] text-center;
    a{
      @apply w-full h-full flex justify-center align-middle py-2 px-3 rounded-md;
    }
  }
}
</style>
