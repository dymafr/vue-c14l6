<script setup lang="ts">
import AppHeader from './components/AppHeader.vue'
import AppFooter from './components/AppFooter.vue'
import AppBoutique from './features/boutique/AppBoutique.vue'
import AppAdmin from './features/admin/AppAdmin.vue'
import { ref, type Component as C } from 'vue'
import type { Page } from './interfaces'
// import { seed } from './data/seed'

const page = ref<Page>('AppBoutique')

const pages: { [s: string]: C } = {
  AppBoutique,
  AppAdmin,
}

function navigate(newPage: Page): void {
  page.value = newPage
}

// seed('projetproducts')
</script>

<template>
  <div class="app-container">
    <AppHeader @navigate="navigate" :page="page" class="header" />
    <div class="app-content">
      <Suspense>
        <Component :is="pages[page]" />
      </Suspense>
    </div>
    <AppFooter class="footer" />
  </div>
</template>

<style lang="scss">
@use './assets/scss/base.scss';
@use './assets/scss/debug.scss';

.app-container {
  height: 100vh;
  display: grid;
  grid-template-areas: 'header' 'app-content' 'footer';
  grid-template-rows: 48px auto 48px;
}

.header {
  grid-area: header;
}

.app-content {
  grid-area: app-content;
}

.footer {
  grid-area: footer;
}
</style>
