<template>
  <div>
    <div v-if="hasImage(article)">
      <img
        class="image"
        :src="getImage(article.image.src)"
        :alt="article.image.alt"
      />
    </div>
    <div v-if="hasAuthor(article)">
      <p class="author">
        By :
        <a :href="article.author.link">{{ article.author.name }}</a
        >,
        {{ formatDateTime(article.createdAt) }}
      </p>
    </div>
    <div v-else>
      <p class="author">
        {{ formatDateTime(article.createdAt) }}
      </p>
    </div>
    <article>
      <nuxt-content :document="article" />
    </article>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  async asyncData({ $content, params }) {
    const article = await $content('articles', params?.slug ?? '').fetch()
    return { article }
  },
  methods: {
    hasImage(article: any) {
      return (article?.image?.src ?? null) !== null
    },
    getImage(src: any) {
      if (src !== null && (typeof src === 'string' || src instanceof String))
        return require(`~/assets/images/${src}`)
      return null
    },
    hasAuthor(article: any) {
      return (article?.author?.name ?? null) !== null
    },
    formatDateTime(dateTime: string) {
      const dateTimeObj = new Date(dateTime)

      return (
        dateTimeObj.getDate() +
        ' ' +
        dateTimeObj.toLocaleString('id-ID', { month: 'short' }) +
        ' ' +
        dateTimeObj.getFullYear()
      )
    },
  },
})
</script>

<style scoped>
.author {
  margin-top: 10px;
  font-size: 1rem;
  text-align: right;
}
</style>
