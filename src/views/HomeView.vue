<template>
  <div class="p-6">
    <!-- Search & Sort -->
    <div class="flex justify-between mb-4">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Cari Product..."
        class="border border-gray-300 p-2 rounded w-1/3"
      />

      <a href="/adddata" class="border border-gray-300 p-2 rounded bg-gray-900 text-white">
        Add Data
      </a>
    </div>

    <!-- Tabel -->
    <div class="relative overflow-x-auto">
      <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
        <thead>
          <tr class="bg-gray-200">
            <th class="border border-gray-300 p-2 w-12">No</th>
            <th class="border border-gray-300 p-2 cursor-pointer" @click="sortByName">
              Title
              <i :class="sortOrder === 'asc' ? 'fas fa-sort-up' : 'fas fa-sort-down'"></i>
            </th>
            <th class="border border-gray-300 p-2">Category</th>
            <th class="border border-gray-300 p-2">Price</th>
            <th class="border border-gray-300 p-2">Rating</th>
            <th class="border border-gray-300 p-2">Desc</th>
            <th class="border border-gray-300 p-2 w-50">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in paginatedData" :key="item.name">
            <td class="border border-gray-300 p-2 text-center">
              {{ index + 1 + (currentPage - 1) * itemsPerPage }}
            </td>
            <td class="border border-gray-300 p-2">
              <img :src="item.image" alt="Product Image" class="w-16 h-16 object-cover" />
              {{ item.title }}
            </td>
            <td class="border border-gray-300 p-2">{{ item.category }}</td>
            <td class="border border-gray-300 p-2">{{ formattedPrice(item.price) }}</td>
            <td class="border border-gray-300 p-2">
              <div class="flex items-center">
                <span v-for="star in 5" :key="star">
                  <i
                    class="fas"
                    :class="
                      star <= Math.round(item.rating.rate)
                        ? 'fa-star text-yellow-500'
                        : 'fa-star text-gray-300'
                    "
                  ></i>
                </span>
              </div>
              <p class="text-center">{{ item.rating.rate }}</p>
              <p class="text-center">Soldout:{{ item.rating.count }}</p>
            </td>
            <td class="border border-gray-300 p-2">{{ truncatedDescription(item.description) }}</td>
            <td class="border border-gray-300 p-2 text-center">
              <button
                class="bg-blue-500 text-white mx-1 border border-gray-300 p-2 rounded-2xl cursor-pointer"
                @click="editdata(item)"
              >
                <i class="fas fa-edit"></i>
              </button>
              <button
                class="bg-red-500 text-white mx-1 border border-gray-300 p-2 rounded-2xl cursor-pointer"
                @click="deletedata(item)"
              >
                <i class="fas fa-trash"></i>
              </button>
              <button
                class="bg-green-500 text-white mx-1 border border-gray-300 p-2 rounded-2xl cursor-pointer"
                @click="viewDetails(item)"
              >
                <i class="fas fa-info-circle"></i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination -->
    <div class="flex justify-between mt-4">
      <div class="flex items-center mb-4">
        <label class="mr-2">Tampilkan:</label>
        <select v-model="itemsPerPage" class="p-2 border border-gray-300 rounded-md">
          <option :value="10">10</option>
          <option :value="30">30</option>
          <option :value="50">50</option>
        </select>
        <span class="ml-2">data per halaman</span>
      </div>
      <div>
        <button
          :disabled="currentPage === 1"
          @click="currentPage--"
          type="button"
          class="p-1 rounded border border-gray-300 mx-2"
        >
          <i class="fas fa-arrow-alt-circle-left"></i>
        </button>
        <span>Halaman {{ currentPage }} dari {{ totalPages }}</span>
        <button
          :disabled="currentPage >= totalPages"
          @click="currentPage++"
          type="button"
          class="p-1 rounded border border-gray-300 mx-2"
        >
          <i class="fas fa-arrow-alt-circle-right"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Swal from 'sweetalert2'
import { API_BASE_URL } from '@/config/config' // Import base URL dari config.js

export default {
  data() {
    return {
      data: [],
      searchQuery: '',
      sortOrder: 'asc',
      currentPage: 1,
      itemsPerPage: 10,
    }
  },
  computed: {
    filtereddata() {
      return this.data
        .filter((item) => item.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        .sort((a, b) =>
          this.sortOrder === 'asc'
            ? a.title.localeCompare(b.title)
            : b.title.localeCompare(a.title),
        )
    },
    paginatedData() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      return this.filtereddata.slice(start, start + this.itemsPerPage)
    },
    totalPages() {
      return Math.ceil(this.filtereddata.length / this.itemsPerPage)
    },
  },
  methods: {
    async fetchdata() {
      try {
        const response = await axios.get(API_BASE_URL + '/products')
        this.data = response.data
        console.log(this.data)
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    sortByName() {
      this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc'
    },
    editdata(item) {
      sessionStorage.setItem('id_data', item.id)
      sessionStorage.setItem('method', 'edit')

      this.$router.push('/adddata')
    },
    async deletedata(data) {
      Swal.fire({
        title: 'Konfirmasi',
        text: `Apakah kamu yakin ingin menghapus ${data.title}?`,
        icon: 'question',
        showCancelButton: true,
        confirmButtonText: 'Ya, Hapus',
        cancelButtonText: 'Batal',
      }).then(async (result) => {
        if (result.isConfirmed) {
          try {
            const response = await axios.delete(API_BASE_URL + '/products/' + data.id)
            console.log('Response:', response.data)

            Swal.fire({
              title: 'Success!',
              text: 'Delete Product Success!',
              icon: 'success',
              confirmButtonText: 'OK',
            })
            this.data = this.data.filter((b) => b.id !== data.id)
          } catch (error) {
            console.error('Error:', error)
            Swal.fire({
              title: 'Gagal!',
              text: 'Terjadi kesalahan, coba lagi!',
              icon: 'error',
              confirmButtonText: 'OK',
            })
          }
        }
      })
    },
    viewDetails(item) {
      sessionStorage.setItem('id_data', item.id)
      sessionStorage.setItem('name_data', item.name)
      sessionStorage.setItem('url_data', item.url)
      sessionStorage.setItem('method', 'detail')

      this.$router.push('/adddata')
    },
    resetfilter() {
      sessionStorage.setItem('method', 'add')
      sessionStorage.setItem('id_data', '')
      sessionStorage.setItem('name_data', '')
      sessionStorage.setItem('url_data', '')
    },
    truncatedDescription(desc) {
      return desc.length > 50 ? desc.slice(0, 150) + '...' : desc
    },
    formattedPrice(price) {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD',
      }).format(price)
    },
  },
  mounted() {
    this.fetchdata()
    this.resetfilter()
  },
}
</script>

<style scoped>
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
