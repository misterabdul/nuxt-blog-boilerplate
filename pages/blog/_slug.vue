<template>
  <div>
    <article><nuxt-content :document="articleContent" /></article>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  async asyncData({ $content, params }) {
    const articleContent = await $content(
      'articles',
      params?.slug ?? ''
    ).fetch()
    return { articleContent }
  },
})
</script>
