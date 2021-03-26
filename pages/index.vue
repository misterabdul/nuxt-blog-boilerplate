<template>
  <div>
    <nuxt-content :document="pageContent" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  async asyncData({ $content }) {
    const pageContent = await $content('pages', 'index').fetch()

    return { pageContent }
  },
})
</script>
