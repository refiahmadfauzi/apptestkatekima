<template>
  <div class="flex items-center justify-between bg-gray-800 text-white p-4">
    <button @click="$emit('toggle-sidebar')" class="md:hidden text-white">
      ☰
      <!-- Ikon menu hamburger -->
    </button>
    <h1 class="text-lg font-semibold">Dashboard</h1>
    <div class="relative">
      <!-- Avatar dan Nama -->
      <div @click.stop="toggleDropdown" class="flex items-center cursor-pointer">
        <img
          src="https://static.vecteezy.com/system/resources/previews/009/397/835/non_2x/man-avatar-clipart-illustration-free-png.png"
          alt="Avatar"
          class="w-12 h-12 rounded-full border border-amber-500"
        />
        <h4 class="my-auto ml-2">Refi Ahmad Fauzi</h4>
      </div>

      <!-- Dropdown Menu -->
      <div
        v-if="dropdownOpen"
        ref="dropdown"
        class="absolute right-0 mt-2 w-48 bg-white border border-gray-300 rounded-lg shadow-lg z-10"
        @click.stop
      >
        <ul class="py-2 text-gray-700">
          <li>
            <a href="#" class="block px-4 py-2 hover:bg-gray-100">Profile</a>
          </li>
          <li>
            <a href="#" class="block px-4 py-2 hover:bg-gray-100">Settings</a>
          </li>
          <li>
            <a href="#" class="block px-4 py-2 text-red-500 hover:bg-gray-100">Logout</a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import { ref, onMounted, onBeforeUnmount } from 'vue'

export default {
  setup() {
    const dropdownOpen = ref(false)
    const dropdown = ref(null)

    const toggleDropdown = () => {
      dropdownOpen.value = !dropdownOpen.value
    }

    const closeDropdown = (event) => {
      if (dropdown.value && !dropdown.value.contains(event.target)) {
        dropdownOpen.value = false
      }
    }

    onMounted(() => {
      document.addEventListener('click', closeDropdown)
    })

    onBeforeUnmount(() => {
      document.removeEventListener('click', closeDropdown)
    })

    return { dropdownOpen, toggleDropdown, dropdown }
  },
}
</script>
