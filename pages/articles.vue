<template>
  <Page title="Articles" name="articles">
    <div class="articles__grid">
      <AnimatedItem
        component="NewsItem"
        v-for="article in articles"
        :key="article.link"
        :article="article"
        :offset="[0.1, 0.9]"
      />
    </div>
  </Page>
</template>

<script>
import articles from "~/assets/data/articles";

articles.sort((a, b) => new Date(b.date) - new Date(a.date));

export default {
  head: {
    meta: [
      {
        hid: "description",
        name: "description",
        content:
          "Featured blog articles about our company, the Challenge 18 project and the global challenges we aim to achieve."
      }
    ]
  },
  data() {
    return { articles };
  }
};
</script>

<style lang="scss">
.articles {
  &__grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    column-gap: 6rem;
    row-gap: 8rem;

    @include respond(mobile-land) {
      grid-template-columns: 1fr;
    }

    @include respond(mobile) {
      row-gap: 6rem;
    }
  }
}
</style>
