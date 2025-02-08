<script setup lang="ts">
import AppShop from './components/Shop/AppShop.vue'
import AppCart from './components/Cart/AppCart.vue'
import { computed, ref } from 'vue'
import type {
  FiltersInterface,
  ProductCartInterface,
  ProductInterface,
  FilterUpdate,
} from '../../interfaces'
import { DEFAULT_FILTERS } from './data/filters'

const products = ref<ProductInterface[]>([])
const cart = ref<ProductCartInterface[]>([])
const filters = ref<FiltersInterface>({ ...DEFAULT_FILTERS })

const fetchedProducts = await (await fetch('https://restapi.fr/api/projetproducts')).json()
if (Array.isArray(fetchedProducts)) {
  products.value = fetchedProducts
} else {
  products.value = [fetchedProducts]
}

function addProductToCart(productId: string): void {
  const product = products.value.find((product) => product._id === productId)
  if (product) {
    const productInCart = cart.value.find((product) => product._id === productId)
    if (productInCart) {
      productInCart.quantity++
    } else {
      cart.value.push({ ...product, quantity: 1 })
    }
  }
}

function removeProductFromCart(productId: string): void {
  const productFromCart = cart.value.find((product) => product._id === productId)!
  if (productFromCart?.quantity === 1) {
    cart.value = cart.value.filter((product) => product._id !== productId)
  } else {
    productFromCart.quantity--
  }
}

function updateFilter(filterUpdate: FilterUpdate) {
  if (filterUpdate.search !== undefined) {
    filters.value.search = filterUpdate.search
  } else if (filterUpdate.priceRange) {
    filters.value.priceRange = filterUpdate.priceRange
  } else if (filterUpdate.category) {
    filters.value.category = filterUpdate.category
  } else {
    filters.value = { ...DEFAULT_FILTERS }
  }
}

const cartEmpty = computed(() => cart.value.length === 0)

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    if (
      product.title.toLocaleLowerCase().startsWith(filters.value.search.toLocaleLowerCase()) &&
      product.price >= filters.value.priceRange[0] &&
      product.price <= filters.value.priceRange[1] &&
      (product.category === filters.value.category || filters.value.category === 'all')
    ) {
      return true
    } else {
      return false
    }
  })
})
</script>

<template>
  <div class="boutique-container" :class="{ 'grid-empty': cartEmpty }">
    <AppShop
      @update-filter="updateFilter"
      :products="filteredProducts"
      :filters="filters"
      @add-product-to-cart="addProductToCart"
      class="shop"
    />
    <AppCart
      v-if="!cartEmpty"
      :cart="cart"
      class="cart"
      @remove-product-from-cart="removeProductFromCart"
    />
  </div>
</template>

<style scoped lang="scss">
.boutique-container {
  display: grid;
  grid-template-columns: 75% 25%;
}
.grid-empty {
  grid-template-columns: 100%;
}
.cart {
  background-color: white;
  border-left: var(--border);
}
</style>
