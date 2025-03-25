<template>
  <div
    :class="[
      'bg-gray-900 text-white w-64 space-y-6 py-7 px-2 inset-y-0 left-0 transform md:relative md:translate-x-0 fixed h-full z-50',
      isOpen ? 'translate-x-0' : '-translate-x-full',
    ]"
    transition="transform ease-in-out duration-300"
  >
    <h2 class="text-2xl font-semibold text-center">Logo</h2>
    <nav>
      <router-link
        to="/"
        class="block py-2.5 px-4 rounded transition duration-200 hover:bg-gray-700"
      >
        Home
      </router-link>
    </nav>
  </div>
</template>

<script>
export default {
  props: {
    isOpen: Boolean, // Sidebar menerima status dari parent
  },
}
</script>
