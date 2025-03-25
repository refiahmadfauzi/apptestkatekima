<template>
  <div class="flex items-center justify-between bg-gray-800 text-white p-4">
    <button @click="$emit('toggle-sidebar')" class="md:hidden text-white">
      ☰
      <!-- Ikon menu hamburger -->
    </button>
    <h1 class="text-lg font-semibold">My App</h1>
  </div>
</template>
