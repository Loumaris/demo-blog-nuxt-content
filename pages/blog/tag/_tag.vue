<template>
  <div
    class="flex lg:h-screen w-screen lg:overflow-hidden xs:flex-col lg:flex-row"
  >
    <div class="relative lg:w-1/2 xs:w-full xs:h-84 lg:h-full post-left">
      TAG-IMAGE
    </div>

    <div class="overlay"></div>
    <div class="absolute top-32 left-32 right-32 text-white">
      <NuxtLink to="/"><Logo /></NuxtLink>
      <div class="mt-16 -mb-3 flex flex-col text-sm">
        <div class="relative lg:w-1/2 xs:w-full xs:h-84 lg:h-full post-left">
          <h1 class="text-4xl font-bold uppercase">
            {{ tag.title }}
          </h1>
          <p class="mb-4 uppercase">lorem ipsum</p>

          <nuxt-content :document="tag" />
        </div>
      </div>
    </div>
    <div
      class="relative xs:py-8 xs:px-8 lg:py-32 lg:px-16 lg:w-1/2 xs:w-full h-full overflow-y-scroll markdown-body post-right custom-scroll"
    >
      <NuxtLink to="/"
        ><p class="hover:underline">Back to All Articles</p></NuxtLink
      >
      <h3 class="mb-4 font-bold text-4xl">Articles tagged {{ tag.name }}:</h3>
      <ul>
        <li
          v-for="article in tag.articles"
          :key="article.slug"
          class="w-full px-2 xs:mb-6 md:mb-12 article-card"
        >
          <NuxtLink
            :to="{ name: 'blog-slug', params: { slug: article.slug } }"
            class="flex transition-shadow duration-150 ease-in-out shadow-sm hover:shadow-md xxlmax:flex-col"
          >
            <img
              v-if="article.thumbnail[0].url"
              class="h-48 xxlmin:w-1/2 xxlmax:w-full object-cover"
              :src="article.thumbnail[0].url"
              :alt="article.title"
            />

            <div
              class="p-6 flex flex-col justify-between xxlmin:w-1/2 xxlmax:w-full"
            >
              <h2 class="font-bold">{{ article.title }}</h2>
              <p>{{ article.excerpt }}</p>
              <p class="font-bold text-gray-600 text-sm">
                {{ formatDate(article.updated_at) }}
              </p>
            </div>
          </NuxtLink>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { gql } from 'graphql-request'

export default {
  async asyncData({ $graphql, params }) {
    const tagQuery = gql`
      query tag($slug: String!) {
        tags(where: { slug: $slug }) {
          title
          slug
          articles {
            title
            updated_at
            slug
            excerpt
            thumbnail {
              url
            }
          }
        }
      }
    `
    const variables = { slug: params.tag }
    const tag = await $graphql.request(tagQuery, variables)
    return { tag: tag.tags[0], variables }
  },
  methods: {
    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' }
      return new Date(date).toLocaleDateString('en', options)
    }
  }
}
</script>
