<script setup lang="ts">
import type { FiltersInterface, ProductInterface, FilterUpdate } from '../../../../interfaces'
import AppShopProductList from './AppShopProductList.vue'
import AppShopFilters from './AppShopFilters.vue'

defineProps<{
  products: ProductInterface[]
  filters: FiltersInterface
}>()

const emit = defineEmits<{
  (e: 'addProductToCart', productId: string): void
  (e: 'updateFilter', updateFilter: FilterUpdate): void
}>()
</script>

<template>
  <div class="d-flex flex-row">
    <AppShopFilters
      :filters="filters"
      :nbr-of-products="products.length"
      @update-filter="emit('updateFilter', $event)"
      class="shop-filter"
    />
    <AppShopProductList
      class="flex-fill scrollable"
      @add-product-to-cart="emit('addProductToCart', $event)"
      :products="products"
    />
  </div>
</template>

<style lang="scss" scoped>
.scrollable {
  overflow-y: auto;
  height: calc(100vh - 96px);
}

.shop-filter {
  flex: 0 0 200px;
}
</style>
