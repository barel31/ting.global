<template>
  <p class="error-message" v-on="$listeners">
    {{ message }}
    <slot />
  </p>
</template>

<script>
export default {
  props: {
    error: null
  },
  computed: {
    message() {
      if (!this.error) return null;
      return typeof this.error === "string"
        ? this.error
        : this.error.response?.data?.msg ||
            this.error.message ||
            "An error occured. Please try again in a few moments.";
    }
  }
};
</script>

<style lang="scss">
.error-message {
  color: $color-error;
  font-weight: 500;
  font-size: 1.7rem;
  text-align: center;
  padding: 0 $padding-sides-mobile;

  @include respond(mobile) {
    font-size: 1.5rem;
  }
}

.form .error-message,
.checkout .error-message,
.modal .error-message,
.button + .error-message,
.editor__wrapper .error-message {
  margin-top: 4rem;

  @include respond(mobile) {
    margin-top: 3rem;
  }
}
</style>
