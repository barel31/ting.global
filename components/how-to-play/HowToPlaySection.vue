<template>
  <section class="how-to-play__section">
    <h2 class="how-to-play__section-title">{{ title }}</h2>
    <div class="how-to-play__section-container">
      <div class="how-to-play__section-content">
        <ul class="diamond-bullet-list">
          <li v-for="item in items" :key="item.text || item">
            <span v-html="item.text || item" />
            <ul v-if="item.subitems">
              <li
                v-for="subitem in item.subitems"
                :key="subitem"
                v-html="subitem"
              />
            </ul>
          </li>
        </ul>
        <p v-if="note" class="how-to-play__section-note">
          <strong>Note:</strong> {{ note }}
        </p>
      </div>
      <img class="how-to-play__img" :src="imageSrc" :alt="title" />
    </div>
  </section>
</template>

<script>
export default {
  props: {
    title: String,
    items: Array,
    note: String,
    image: String
  },
  computed: {
    imageSrc() {
      return require(`~/assets/images/how-to-play/${this.image}`);
    }
  }
};
</script>

<style lang="scss">
.how-to-play {
  &__section {
    &:not(:last-child) {
      margin-bottom: 6rem;

      @include respond(tablet) {
        margin-bottom: 7.5rem;
      }

      @include respond(mobile) {
        margin-bottom: 6rem;
      }
    }
  }

  &__section-container {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;

    @include respond(tablet) {
      flex-direction: column;
    }
  }

  &__section-content {
    font-size: 1.7rem;
    line-height: 1.6;

    @include respond(mobile) {
      font-size: 1.55rem;
      line-height: 1.7;
    }

    & > ul {
      margin-left: 1rem;

      @include respond(mobile) {
        margin-left: 0;
      }
    }
  }

  &__section-title {
    font-size: 3rem;
    color: $color-blue-2;
    margin-bottom: 2rem;
    font-weight: 700;

    @include respond(mobile) {
      font-size: 2.4rem;
      margin-bottom: 1.75rem;
    }
  }

  ul {
    & > li {
      &:not(:last-child) {
        margin-bottom: 1rem;

        @include respond(mobile) {
          margin-bottom: 0.75rem;
        }
      }
    }

    ul {
      margin-left: 2.25rem;
      margin-top: 1rem;

      @include respond(mobile) {
        margin-left: 1.75rem;
      }
    }

    strong {
      font-weight: 600;
    }
  }

  &__section-note {
    margin-top: 2rem;

    @include respond(mobile) {
      margin-top: 1.75rem;
    }
  }

  &__img {
    display: block;
    width: 27.5vw;
    border-radius: 0.8rem;
    box-shadow: $boxshadow2;
    margin-left: 5rem;
    flex-shrink: 0;

    @include respond(tablet) {
      width: 100%;
      max-width: 60rem;
      margin: auto;
      margin-top: 3rem;
    }

    @include respond(mobile) {
      margin-top: 2rem;
    }
  }
}
</style>
