<template>
  <div>
    <h1>Search results for : {{ searchQuery }}</h1>
    <ul v-if="isArray(articles)">
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
    <div v-else>{{ pageQuery === 1 ? 'No result.' : 'Empty page.' }}</div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      searchQuery: '',
      pageQuery: 1,
      articles: {},
    }
  },
  watch: {
    $route() {
      this.fetchContent()
    },
  },
  mounted() {
    this.fetchContent()
  },
  methods: {
    fetchContent() {
      this.searchQuery = String(this.$route.query?.q ?? '')
      this.pageQuery = Number(this.$route.query?.page ?? 1)
      this.loadArticles(this.searchQuery, this.pageQuery - 1)
        .then((articles) => {
          this.articles = articles
        })
        .catch(() => {
          this.articles = {}
        })
    },
    isArray(input: any): boolean {
      if (Array.isArray(input) && input?.length > 0) return true
      return false
    },
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
    async loadArticles(searchQuery: any, pageQuery: number) {
      return await this.$content('articles')
        .search('title', searchQuery)
        .only(['title', 'description', 'slug', 'createdAt'])
        .sortBy('createdAt', 'desc')
        .limit(20)
        .skip(20 * pageQuery)
        .fetch()
    },
  },
})
</script>
