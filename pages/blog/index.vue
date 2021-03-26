<template>
  <div>
    <h1>Blog Posts</h1>
    <ul>
      <li v-for="article of articles" :key="article.slug">
        <nuxt-link :to="{ name: 'blog-slug', params: { slug: article.slug } }">
          <div>
            <h2>{{ article.title }}</h2>
            <p>{{ article.description }}</p>
            <p>{{ formatDateTime(article.createdAt) }}</p>
          </div>
        </nuxt-link>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  async asyncData({ $content }) {
    const articles = await $content('articles')
      .only(['title', 'description', 'slug', 'createdAt'])
      .sortBy('createdAt', 'desc')
      .limit(20)
      .fetch()
    return { articles }
  },
  methods: {
    formatDateTime(dateTime: string) {
      const dateTimeObj = new Date(dateTime)

      return (
        dateTimeObj.getDate() +
        ' ' +
        dateTimeObj.toLocaleString('id-ID', { month: 'short' }) +
        ' ' +
        dateTimeObj.getFullYear() +
        ', ' +
        dateTimeObj.getHours() +
        ':' +
        dateTimeObj.getMinutes() +
        ':' +
        dateTimeObj.getSeconds()
      )
    },
  },
})
</script>
