<template>
  <div class="task-form__emoji-selector" :id="`emoji-${task.id}`">
    <label class="task-form__selector-label">Emoji</label>
    <button
      v-if="isTemplateEditable"
      @click="showPicker = !showPicker"
      class="task-form__emoji-button"
    >
      <span v-if="!task.emoji" class="task-form__no-emoji">
        Click to select
      </span>
      <span v-else class="task-form__selected-emoji">
        {{ task.emoji }}
      </span>
    </button>
    <span v-else class="task-form__selected-emoji">
      {{ task.emoji }}
    </span>
    <div v-if="showPicker" class="task-form__emoji-wrapper">
      <VEmojiPicker @select="selectEmoji" />
    </div>
  </div>
</template>

<script>
import { VEmojiPicker } from "v-emoji-picker";

export default {
  components: { VEmojiPicker },
  inject: ["task", "isTemplateEditable"],
  data() {
    return {
      showPicker: false
    };
  },
  methods: {
    selectEmoji(emoji) {
      this.task.emoji = emoji.data;
      this.showPicker = false;
    },
    closeOnClick(event) {
      if (!event.target.closest(`#emoji-${this.task.id}`)) {
        this.showPicker = false;
      }
    },
    closeOnEscPress(event) {
      if (event.key === "Escape") {
        this.showPicker = false;
      }
    }
  },
  mounted() {
    document.addEventListener("click", this.closeOnClick);
    document.addEventListener("keydown", this.closeOnEscPress);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.closeOnClick);
    document.removeEventListener("keydown", this.closeOnEscPress);
  }
};
</script>

<style lang="scss">
.task-form {
  &__emoji-selector {
    position: relative;
    text-align: center;

    .grid-emojis {
      row-gap: 1rem;
    }

    .emoji-picker {
      box-shadow: $boxshadow2;
    }

    .emoji {
      overflow: visible !important;
    }
  }

  &__emoji-button {
    outline: none;
  }

  &__no-emoji {
    font-size: 1.4rem;

    @include respond(mobile) {
      font-size: 1.3rem;
    }
  }

  &__selected-emoji {
    font-size: 2rem;
  }

  &__emoji-wrapper {
    position: absolute;
    top: calc(100% + 1rem);
    left: 50%;
    transform: translateX(-50%);
    z-index: 15;
  }
}
</style>
