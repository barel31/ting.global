<template>
  <div class="certification-pdf">
    <div class="certification-pdf__preview">Preview:</div>
    <section class="certification-pdf__content">
      <CertificationTemplate
        :certification="certification"
        :certificationSignature="certificationSignature"
        :name="name"
      />
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: '[NAME]',
    };
  },
  props: {
    certification: {
      type: Object,
      required: true,
    },
    certificationSignature: {
      type: String,
      required: true,
    },
  },
  methods: {
    async generatePdf(name) {
      return;
    },
  },
};
</script>

<style lang="scss">
.certification-pdf {
  &__preview {
    font-size: 3rem;
    font-weight: bold;
    text-align: center;
    margin: 1rem;
  }

  &__content {
    margin: auto;
  }
}
</style>
