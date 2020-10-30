<template>
  <article
    class="flex lg:h-screen w-screen lg:overflow-hidden xs:flex-col lg:flex-row"
  >
    <div class="relative lg:w-1/2 xs:w-full xs:h-84 lg:h-full post-left">
      <img
        :src="article.thumbnail[0].url"
        :alt="article.title"
        class="absolute h-full w-full object-cover"
      />
      <div class="overlay"></div>
      <div class="absolute top-32 left-32 text-white">
        <NuxtLink to="/"><Logo /></NuxtLink>
        <div class="mt-16 -mb-3 flex uppercase text-sm">
          <p class="mr-3">
            {{ formatDate(article.updated_at) }}
          </p>
          <span class="mr-3">•</span>
          <p>Author</p>
        </div>
        <h1 class="text-6xl font-bold">{{ article.title }}</h1>
        <span v-for="(tag, id) in article.tags" :key="id">
          <NuxtLink :to="`/blog/tag/${tag.slug}`">
            <span
              class="truncate uppercase tracking-wider font-medium text-ss px-2 py-1 rounded-full mr-2 mb-2 border border-light-border dark:border-dark-border transition-colors duration-300 ease-linear"
            >
              {{ tag.title }}
            </span>
          </NuxtLink>
        </span>
      </div>
      <div class="flex absolute top-3rem right-3rem">
        <NuxtLink
          to="/"
          class="mr-8 self-center text-white font-bold hover:underline"
        >
          All articles
        </NuxtLink>

        <AppSearchInput />
      </div>
    </div>
    <div
      class="relative xs:py-8 xs:px-8 lg:py-32 lg:px-16 lg:w-1/2 xs:w-full h-full overflow-y-scroll markdown-body post-right custom-scroll"
    >
      <h1 class="font-bold text-4xl">{{ article.title }}</h1>
      <p v-html="$md.render(article.body)"></p>
      <p class="pb-4">
        Post last updated: {{ formatDate(article.created_at) }}
      </p>
      <!-- table of contents -->
      <nav class="pb-6">
        <ul>
          <li
            v-for="link of article.toc"
            :key="link.id"
            :class="{
              'font-semibold': link.depth === 2
            }"
          >
            <nuxtLink
              :to="`#${link.id}`"
              class="hover:underline"
              :class="{
                'py-2': link.depth === 2,
                'ml-2 pb-2': link.depth === 3
              }"
              >{{ link.text }}</nuxtLink
            >
          </li>
        </ul>
      </nav>
    </div>
  </article>
</template>
<script>
import { gql } from 'graphql-request'
export default {
  async asyncData({ $graphql, params }) {
    const query = gql`
      query Article($slug: String!) {
        articles(where: { slug: $slug }) {
          title
          created_at
          updated_at
          excerpt
          body
          thumbnail {
            url
          }
          tags {
            slug
            title
          }
          seo {
            title
            description
            keywords
          }
        }
      }
    `
    const variables = { slug: params.slug }
    const article = await $graphql.request(query, variables)
    return { article: article.articles[0], variables }
  },
  methods: {
    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' }
      return new Date(date).toLocaleDateString('en', options)
    }
  },
  head() {
    return {
      title: this.article.seo.title,
      meta: [
        {
          hid: 'title',
          property: 'title',
          content: this.article.seo.title
        },
        {
          hid: 'description',
          property: 'description',
          content: this.article.seo.description
        },
        {
          hid: 'keywords',
          property: 'keywords',
          content: this.article.seo.keywords
        },
        {
          hid: 'og-title',
          property: 'og:title',
          content: this.article.seo.title
        },
        {
          hid: 'og-description',
          property: 'og:description',
          content: this.article.seo.description
        },
        {
          hid: 'og-keywords',
          property: 'og:keywords',
          content: this.article.seo.keywords
        }
      ]
    }
  }
}
</script>
<style>
.nuxt-content p {
  margin-bottom: 20px;
}
.nuxt-content h2 {
  font-weight: bold;
  font-size: 28px;
}
.nuxt-content h3 {
  font-weight: bold;
  font-size: 22px;
}
.icon.icon-link {
  background-image: url('~assets/svg/icon-hashtag.svg');
  display: inline-block;
  width: 20px;
  height: 20px;
  background-size: 20px 20px;
}
</style>
