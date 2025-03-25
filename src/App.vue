<template>
  <div class="flex h-full">
    <!-- Sidebar -->
    <Sidebar :isOpen="isSidebarOpen" class="w-64 text-white fixed md:relative h-screen" />

    <div class="flex flex-col flex-1">
      <!-- Navbar -->
      <Navbar @toggle-sidebar="toggleSidebar" class="bg-gray-800 text-white p-4" />

      <!-- Content -->
      <div class="p-4 bg-gray-100">
        <router-view></router-view>
      </div>
    </div>

    <!-- Overlay (untuk menutup sidebar di mobile) -->
    <div
      v-if="isSidebarOpen"
      class="fixed inset-0 bg-black opacity-50 md:hidden"
      @click="isSidebarOpen = false"
    ></div>
  </div>
</template>

<script>
import Sidebar from '@/components/Sidebar.vue'
import Navbar from '@/components/Navbar.vue'

export default {
  components: { Sidebar, Navbar },
  data() {
    return {
      isSidebarOpen: false, // Default tertutup di mobile
    }
  },
  methods: {
    toggleSidebar() {
      this.isSidebarOpen = !this.isSidebarOpen
    },
  },
}
</script>
