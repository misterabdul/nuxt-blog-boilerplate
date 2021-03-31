<template>
  <div>
    <h1 class="h1">Search results for : {{ searchQuery }}</h1>
    <ul v-if="isArray(articles)">
      <li v-for="article of articles" :key="article.slug">
        <blog-item :article="article" />
      </li>
    </ul>
    <div v-else>{{ pageQuery === 1 ? 'No result.' : 'Empty page.' }}</div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import BlogItem from '~/components/BlogItem.vue'

export default Vue.extend({
  components: { BlogItem },
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
    async loadArticles(searchQuery: any, pageQuery: number) {
      return await this.$content('articles')
        .search('title', searchQuery)
        .sortBy('createdAt', 'desc')
        .limit(20)
        .skip(20 * pageQuery)
        .fetch()
    },
  },
})
</script>

<style scoped>
.h1 {
  font-size: 2rem;
  font-weight: 200;
  margin-bottom: 20px;
}
</style>
