<template>
  <div class="max-w mx-auto p-6 bg-white rounded-lg shadow-md">
    <h1 class="text-xl font-bold mb-4">{{ title }}</h1>
    <div v-if="method != 'add'" class="flex items-center mb-4">
      <label class="mr-2">Tampilkan:</label>
      <select v-model="selected" class="p-2 border border-gray-300 rounded-md" @change="getbyid()">
        <option v-for="(item, index) in data" :key="item.id" :value="item.id">
          {{ item.title }}
        </option>
      </select>
    </div>
    <form @submit.prevent="addProduct">
      <div class="mb-3">
        <label class="block text-gray-700">Title:</label>
        <input v-model="product.title" type="text" class="w-full p-2 border rounded" required />
      </div>

      <div class="mb-3">
        <label class="block text-gray-700">Price ($):</label>
        <input
          v-model="product.price"
          type="number"
          step="0.01"
          class="w-full p-2 border rounded"
          required
        />
      </div>

      <div class="mb-3">
        <label class="block text-gray-700">Description:</label>
        <textarea
          v-model="product.description"
          class="w-full p-2 border rounded"
          required
          rows="5"
        ></textarea>
      </div>

      <div class="mb-3">
        <label class="block text-gray-700">Category:</label>
        <input v-model="product.category" type="text" class="w-full p-2 border rounded" required />
      </div>

      <div class="mb-3">
        <label class="block text-gray-700">Image URL:</label>
        <input v-model="product.image" type="url" class="w-full p-2 border rounded" required />
        <img
          v-if="product.image"
          :src="product.image"
          class="h-48 w-96 object-contain my-2"
          alt=""
          srcset=""
        />
        <img
          v-else
          src="https://images.unsplash.com/photo-1554629947-334ff61d85dc?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1000&h=1000&q=90"
          class="h-48 w-96 object-contain my-2"
          alt=""
          srcset=""
        />
      </div>

      <button
        v-if="method != 'detail'"
        type="submit"
        class="w-full bg-blue-500 text-white p-2 rounded hover:bg-blue-600 cursor-pointer"
      >
        {{ title }}
      </button>
    </form>
  </div>
</template>

<script>
import axios from 'axios'
import Swal from 'sweetalert2'
import { API_BASE_URL } from '@/config/config' // Import base URL dari config.js

export default {
  data() {
    return {
      product: {
        title: '',
        price: '',
        description: '',
        category: '',
        image: '',
      },
      method: '',
      title: '',
      data: [],
    }
  },
  methods: {
    async fetchdataall() {
      try {
        const response = await axios.get(API_BASE_URL + '/products')
        this.data = response.data
        console.log(this.data)
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    async fetchdata() {
      var id = sessionStorage.getItem('id_data')
      this.method = sessionStorage.getItem('method')
      if (this.method != 'add') {
        if (this.selected) {
          id = this.selected
        }
        try {
          const response = await axios.get(API_BASE_URL + '/products/' + id)
          const data = response.data

          this.product.title = data.title
          this.product.price = data.price
          this.product.description = data.description
          this.product.category = data.category
          this.product.image = data.image
          this.selected = data.id

          this.title = this.method == 'edit' ? 'Edit Product' : 'Detail Product'
        } catch (error) {
          console.error('Error fetching data:', error)
        }
      } else {
        this.title = 'Add Product'
      }
    },
    async addProduct() {
      let id = sessionStorage.getItem('id_data')
      if (this.method == 'add') {
        try {
          const response = await axios.post(API_BASE_URL + '/products/', this.product)
          console.log('Response:', response.data)

          Swal.fire({
            title: 'Success!',
            text: 'Add Product Success!',
            icon: 'success',
            confirmButtonText: 'OK',
          })
          this.$router.push('/')
        } catch (error) {
          console.error('Error:', error)
          Swal.fire({
            title: 'Gagal!',
            text: 'Terjadi kesalahan, coba lagi!',
            icon: 'error',
            confirmButtonText: 'OK',
          })
        }
      } else {
        try {
          const response = await axios.put(API_BASE_URL + '/products/' + id, this.product)
          console.log('Response:', response.data)

          Swal.fire({
            title: 'Success!',
            text: 'Update Product Success!',
            icon: 'success',
            confirmButtonText: 'OK',
          })
          this.$router.push('/')
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
    },
    getbyid() {
      this.fetchdata()
    },
  },
  mounted() {
    this.fetchdata()
    this.fetchdataall()
  },
}
</script>

<style>
body {
  background-color: #f3f4f6;
}
</style>
