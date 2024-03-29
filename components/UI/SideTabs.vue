<template>
  <div :class="classes">
    <button
      v-for="tab in tabs"
      :key="tab.id"
      class="side-tabs__tab"
      @click="$emit('change', tab.value)"
    >
      <input type="radio" :checked="selectedTab === tab.value" />
      <label>{{ tab.label }}</label>
    </button>
  </div>
</template>

<script>
export default {
  model: {
    prop: "selectedTab",
    event: "change"
  },
  props: {
    selectedTab: [Number, String],
    tabs: Array,
    tabletCols: {
      type: Number,
      default: 6
    },
    mobileCols: {
      type: Number,
      default: 3
    }
  },
  computed: {
    classes() {
      return [
        "side-tabs",
        `side-tabs--tablet-${this.tabletCols}`,
        `side-tabs--mobile-${this.mobileCols}`
      ];
    }
  }
};
</script>

<style lang="scss">
.side-tabs {
  box-shadow: $boxshadow2;
  display: grid;
  grid-template-columns: 1fr;
  overflow: hidden;
  position: relative;
  background-color: rgba(#eee, 0.5);

  @include respond(desktop) {
    border-radius: 0.8rem;
  }

  @include respond(tablet) {
    grid-template-columns: repeat(6, 1fr);
  }

  @include respond(mobile-land) {
    grid-template-columns: repeat(3, 1fr);
  }

  @include respond(mobile) {
    margin-bottom: 3rem;
  }

  &__tab {
    font: inherit;
    border: none;
    outline: none;

    @include respond(tablet) {
      border-bottom: 0.1rem solid #ccc;
    }

    &:not(:last-child) {
      @include respond(desktop) {
        border-bottom: 0.1rem solid #ccc;
      }
    }

    &:not(:nth-child(6n)) {
      @include respond(tablet) {
        border-right: 0.1rem solid #ccc;
      }

      @include respond(mobile-land) {
        border-right: none;
      }
    }

    &:not(:nth-child(3n)) {
      @include respond(mobile-land) {
        border-right: 0.1rem solid #ccc;
      }
    }

    &:nth-child(6n + 1):nth-last-child(-n + 6),
    &:nth-child(6n + 1):nth-last-child(-n + 6) ~ & {
      @include respond(tablet) {
        border-bottom: none;
      }

      @include respond(mobile-land) {
        border-bottom: 0.1rem solid #ccc;
      }
    }

    &:nth-child(3n + 1):nth-last-child(-n + 3),
    &:nth-child(3n + 1):nth-last-child(-n + 3) ~ & {
      @include respond(mobile-land) {
        border-bottom: none !important;
      }
    }

    input {
      display: none;
    }

    label {
      display: block;
      cursor: pointer;
      width: 100%;
      height: 100%;
      border: 0.3rem solid transparent;
      padding: 1.5rem;
      text-align: center;
      font-weight: 600;
      font-size: 1.8rem;
      background-color: #fff;
      transition: all 0.5s;
      position: relative;

      @include respond(mobile) {
        font-size: 1.6rem;
      }
    }

    &:hover label,
    &:focus label {
      background-color: rgba($color-azure-light, 0.8);
      z-index: 1;
    }

    &:first-child label {
      @include respond(desktop) {
        border-radius: 0.8rem 0.8rem 0 0;
      }
    }

    &:last-child label {
      @include respond(desktop) {
        border-radius: 0 0 0.8rem 0.8rem;
      }
    }

    &:first-child:last-child label {
      @include respond(desktop) {
        border-radius: 0.8rem;
      }
    }

    input:checked + label {
      background-color: rgba($color-azure-light, 0.8);
      border-color: $color-azure;
      z-index: 1;
    }
  }
}

[style="direction: rtl;"] .side-tabs {
  &__tab {
    &:not(:nth-child(6n)) {
      border-right: none;

      @include respond(tablet) {
        border-left: 0.1rem solid #ccc;
      }

      @include respond(mobile-land) {
        border-left: none;
      }
    }

    &:not(:nth-child(3n)) {
      border-right: none;

      @include respond(mobile-land) {
        border-left: 0.1rem solid #ccc;
      }
    }
  }
}
</style>
