<script setup>
import { reactive, computed, watch } from 'vue'
const defaultResults = {
  products: [],
}
const state = reactive({
  term: null,
  results: { ...defaultResults },
  req: null,
  loading: false,
})

const findProducts = async term => {
  state.loading = true
  try {
    let res = await fetch(`https://dummyjson.com/products/search?q=${term}`)
    let decoded = await res.json()
    state.results = decoded
  } catch (e) {
    console.error(e)
    alert('There was an error!')
  }
  state.loading = false
}

const searchTerm = computed({
  get() {
    return state.term
  },
  set(newTerm) {
    state.term = newTerm
    if (state.req) clearTimeout(state.req)
    if (newTerm) {
      state.req = setTimeout(findProducts, 300, newTerm)
    } else {
      state.results = { ...defaultResults }
    }
  },
})
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div class="loading" v-if="state.loading">LOADING</div>
    <ul class="list-disc" v-else>
      <li v-for="product in state.results.products">{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
