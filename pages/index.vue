<template>
  <div class="m-8">
    <TheHeader />

    <b-container>
      <b-row v-for="stage in lanes" :key="stage.id" class="mb-4">
        <b-col>
          <h1 class="font-bold text-4xl">{{ stage.title }}</h1>
          <ul class="flex flex-wrap">
            <li
              v-for="article of stage.articles"
              :key="article.slug"
              class="xs:w-full md:w-1/2 px-2 xs:mb-6 md:mb-12 article-card"
            >
              <NuxtLink
                :to="{ name: 'blog-slug', params: { slug: article.slug } }"
                class="flex transition-shadow duration-150 ease-in-out shadow-sm hover:shadow-md xxlmax:flex-col"
              >
                <img
                  v-if="article.thumbnail[0].url"
                  class="h-48 xxlmin:w-1/2 xxlmax:w-full object-cover"
                  :src="article.thumbnail[0].url"
                />

                <div
                  class="p-6 flex flex-col justify-between xxlmin:w-1/2 xxlmax:w-full"
                >
                  <h2 class="font-bold">{{ article.title }}</h2>
                  <p>by Author</p>
                  <p class="font-bold text-gray-600 text-sm">
                    {{ article.excerpt }}
                  </p>
                </div>
              </NuxtLink>
            </li>
          </ul>
        </b-col>
      </b-row>
    </b-container>

    <h3 class="mb-4 font-bold text-2xl uppercase text-center">Topics</h3>
    <ul class="flex flex-wrap mb-4 text-center">
      <li
        v-for="tag of tags"
        :key="tag.slug"
        class="xs:w-full md:w-1/3 lg:flex-1 px-2 text-center"
      >
        <NuxtLink :to="`/blog/tag/${tag.slug}`" class="">
          <p
            class="font-bold text-gray-600 uppercase tracking-wider font-medium text-ss"
          >
            {{ tag.title }}
          </p>
        </NuxtLink>
      </li>
    </ul>
    <Footer></Footer>
  </div>
</template>

<script>
import { gql } from 'graphql-request'

export default {
  async asyncData({ $graphql, params }) {
    const homeQuery = gql`
      query home {
        home {
          stages {
            id
            title
            articles {
              categories {
                slug
              }
              slug
              title
              excerpt
              thumbnail {
                url
              }
            }
          }
        }
      }
    `

    const tagsQuery = gql`
      query navigation {
        tags {
          slug
          title
        }
      }
    `
    const tags = await $graphql.request(tagsQuery)

    const lanes = await $graphql.request(homeQuery)
    return { lanes: lanes.home.stages, tags: tags.tags }
  },

  methods: {
    categoryUrl(categorySlug, slug) {
      return categorySlug + '/' + slug
    }
  }
}
</script>

<style class="postcss">
.article-card {
  border-radius: 8px;
}
.article-card a {
  background-color: #fff;
  border-radius: 8px;
}
.article-card img div {
  border-radius: 8px 0 0 8px;
}
</style>
