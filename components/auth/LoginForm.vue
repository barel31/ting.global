<template>
  <form class="form" @submit.prevent="submitHandler">
    <div v-if="!verificationMode" class="form__field">
      <label for="phone" class="form__label">Phone number</label>
      <VuePhoneNumberInput
        class="phone-number-input"
        id="phone"
        v-model="phoneInput.value"
        @update="updatePhoneNumber"
        color="#007bff"
        :border-radius="8"
        :show-code-on-list="true"
        :no-flags="true"
      />
    </div>
    <div v-else class="verification">
      <p class="verification__text">
        Enter the 6-digit code that was sent to phone number
        {{ phoneInput.formatted }}
      </p>
      <input
        :value="code"
        @input="codeInputHandler"
        @keydown.enter.prevent
        class="verification__input"
        :maxlength="6"
        :readonly="loading"
        autofocus
        ref="code"
      />
      <p class="verification__subtext">The code will expire in 10 minutes.</p>
    </div>
    <div>
      <label for="password" class="form__label">Password</label>
      <input
        v-model="formData.password"
        placeholder="Password"
        class="password"
        color="#007bff"
        required=true
        
      />
    </div>
    <BaseButton v-if="!verificationMode" variant="blue">Login</BaseButton>
    <BaseSpinner v-if="loading" />
    <ErrorMessage v-else-if="error" :error="error" />
    <BaseButton
      v-if="verificationMode && error"
      @click="verificationMode = false"
      variant="blue"
    >
      Retry login
    </BaseButton>
  </form>
</template>

<script>
import VuePhoneNumberInput from "vue-phone-number-input";

export default {
  components: { VuePhoneNumberInput },
  data() {
    return {
      formData: {
        phone: "",
        password:""
      },
      phoneInput: {
        value: "",
        formatted: null,
        isValid: false
      },
      loading: false,
      error: null,
      verificationMode: false,
      code: ""
    };
  },
  methods: {
    updatePhoneNumber(data) {
      this.formData.phone = data.formattedNumber;
      this.phoneInput.formatted = data.formatInternational;
      this.phoneInput.isValid = data.isValid;
    },
    async submitHandler() {
      if (!this.phoneInput.isValid) {
        if (this.phoneInput.value) {
          this.error = "Invalid phone number";
        }
        return;
      }
      this.loading = true;
      this.error = null;
      try {
        await this.$store.dispatch("auth", {
          mode: "signIn",
          data: this.formData
        });
        // await this.$axios.$post("/api", { signIn: this.formData });
        // this.verificationMode = true;
      } catch (error) {
        console.log(error.response.data);
        this.error = error;
      }
      this.loading = false;
    },
    codeInputHandler(event) {
      event.target.value = event.target.value.replace(/[^0-9]/g, "");
      this.code = event.target.value;
    },
    async verifyCode() {
      this.loading = true;
      this.error = null;
      try {
        await this.$axios.$post("/api", {
          verifySignIn: {
            userId: this.formData.phone,
            password: this.formData.password,
            code: this.code
          }
        });
        // await this.$store.dispatch("auth", {
        //   userId: this.formData.phone,
        //   code: this.code
        // });
      } catch (error) {
        this.error = error;
      }
      this.loading = false;
    }
  },
  watch: {
    verificationMode(value) {
      setTimeout(() => {
        this.error = null;
        if (value) {
          this.$refs.code.focus();
        }
      }, 10);
      this.phoneInput.value = "";
      this.code = "";
    },
    code(value) {
      if (value.length === 6) {
        this.verifyCode();
      } else {
        this.error = null;
      }
    }
  },
  mounted() {
    this.$el.querySelector(".input-tel__input").focus();
    setTimeout(() => {
      const { top } = this.$el.getBoundingClientRect();
      window.scrollTo(0, window.scrollY + top - 150);
    }, 10);
  }
};
</script>

<style lang="scss">
.verification {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;

  &__text {
    font-size: 2rem;
    font-weight: 500;
    margin-bottom: 2rem;

    @include respond(mobile) {
      font-size: 1.7rem;
    }
  }

  &__input {
    outline: none;
    border: 2px solid #ccc;
    border-radius: 0.5rem;
    width: 22.5rem;
    padding: 0.75rem 0;
    font: inherit;
    font-size: 3rem;
    letter-spacing: 1rem;
    text-align: center;
    display: block;
    transition: all 0.5s;

    @include respond(mobile) {
      font-size: 2.8rem;
    }

    &:focus {
      border-color: $color-azure;
    }
  }

  &__subtext {
    font-size: 1.4rem;
    margin-top: 2rem;

    @include respond(mobile) {
      font-size: 1.3rem;
    }
  }

  & + .error-message:not(:last-child) {
    margin-bottom: 3.5rem;

    @include respond(mobile) {
      margin-bottom: 2.5rem;
    }
  }
}
.password{
    border-radius: 8px;
    caret-color: rgb(0, 123, 255);
    cursor: pointer;
    background-color: #fff;
    -webkit-transition-duration: .3s;
    transition-duration: .3s;
    position: relative;
    width: 100%;
    padding: 0 12px;
    font-weight: 400;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    outline: none;
    border: 1px solid #ccc;
    font-size: 16px;
    z-index: 0;
    margin-left: -1px;
    height: 50px;
    min-height: 50px;
}
</style>
