<template>
  <div class="task-form">
    <div class="task-form__top">
      <h3 class="task-form__title">
        {{ taskTitle }}
      </h3>
      <div class="task-form__top-icons" v-if="isTemplateEditable">
        <IconButton
          type="mark"
          :filled="task.isBonus"
          @click="task.isBonus = !task.isBonus"
        />
        <IconButton type="delete" @click="deleteTask(taskIndex)" />
      </div>
    </div>
    <div class="task-form__selectors">
      <TaskPointsSelector />
      <TimeSelector v-model="task.time" />
      <TaskEmojiSelector />
    </div>
    <div
      v-for="(option, optionIndex) in task.options"
      :key="option.id"
      class="task-form__option"
    >
      <input
        v-if="!templateOnlyMode"
        type="radio"
        :id="option.id"
        class="radio-input"
        :value="option.text"
        v-model="task.selection"
      />
      <i v-if="templateOnlyMode" class="task-form__option-icon fas fa-gem" />
      <label v-else :for="option.id" class="radio-label">
        <span class="radio-button" />
      </label>
      <label
        v-if="editedOption !== `${task.id}-${option.id}`"
        :for="option.id"
        class="task-form__text"
      >
        <span class="task-form__text-wrapper">
          <span
            v-for="paragraph in convertedOptions[optionIndex]"
            :key="paragraph"
            v-html="paragraph"
            v-linkified
          />
        </span>
        <div class="task-form__option-actions" v-if="isTemplateEditable">
          <div class="task-form__option-actions-wrapper">
            <IconButton
              type="edit"
              @click="setEditedOption(task.id, option.id)"
              class="task-form__option-button"
            />
            <IconButton
              type="delete"
              @click="deleteOption(optionIndex)"
              class="task-form__option-button"
            />
          </div>
        </div>
      </label>
      <textarea-autosize
        v-else
        :value="option.text"
        @input="editOption($event, optionIndex)"
        @keydown.native.enter.exact.prevent="finishEditOption"
        @keydown.native.esc="finishEditOption"
        class="task-form__option-edit"
        :id="`edit-${option.id}`"
        placeholder="Start typing here..."
        :rows="1"
      />
    </div>
    <textarea-autosize
      v-if="isTemplateEditable"
      :value="task.extraInput"
      @input="updateExtraInput"
      @keydown.native.enter.exact.prevent="addOption"
      class="task-form__extra"
      :placeholder="extraInputPlaceholder"
      :rows="1"
    />
  </div>
</template>

<script>
import { convertTaskText, stripHTML } from "../../assets/util/functions";
import { taskTranslations } from "../../assets/util/options";
import uniqid from "uniqid";

export default {
  props: {
    task: Object
  },
  inject: [
    "data",
    "selectedDayTasks",
    "deleteTask",
    "templateOnlyMode",
    "getEditedOption",
    "setEditedOption",
    "finishEditOption",
    "setConfirmModal",
    "setTransition",
    "isTemplateEditable"
  ],
  computed: {
    taskLabel() {
      return taskTranslations[this.data.language] || "Task";
    },
    taskIndex() {
      return this.selectedDayTasks().indexOf(this.task);
    },
    editedOption() {
      return this.getEditedOption();
    },
    convertedOptions() {
      return this.task.options
        .map(option => convertTaskText(option.text))
        .map(option => option.split("\n").filter(p => !!p.trim()));
    },
    taskTitle() {
      let title = `${this.taskLabel} ${this.taskIndex + 1}`;
      if (this.task.isBonus) {
        title += " (bonus)";
      }
      return title;
    },
    isBonus() {
      return this.task.isBonus;
    },
    optionInput() {
      if (!this.editedOption) return;
      const [taskId, optionId] = this.editedOption.split("-");
      return taskId === this.task.id
        ? this.$el.querySelector(`#edit-${optionId}`)
        : null;
    },
    extraInputPlaceholder() {
      return process.client
        ? window.innerWidth > 600
          ? "Type and press Enter to add a new option..."
          : "Enter new option here..."
        : null;
    }
  },
  methods: {
    updateExtraInput(value) {
      this.task.extraInput = stripHTML(value);
    },
    addOption() {
      const newOptionText = stripHTML(this.task.extraInput).trim();
      this.task.extraInput = "";
      if (newOptionText) {
        this.task.selection = newOptionText;
        this.task.options.push({ id: uniqid(), text: newOptionText });
        this.setTransition(null);
      }
    },
    editOption(value, optionIndex) {
      this.task.options[optionIndex].text = stripHTML(value);
      this.task.selection = stripHTML(value);
    },
    deleteOption(optionIndex) {
      this.setConfirmModal(
        "Are you sure you want to delete this option? This action is irreversible.",
        () => {
          this.task.options.splice(optionIndex, 1);
          this.setTransition("task");
        },
        this.task.options[optionIndex].text.length < 50
      );
    }
  },
  watch: {
    taskIndex(value) {
      if (!this.task.isBonus) {
        this.task.points = value + 1;
      }
    },
    isBonus(value) {
      this.task.points = value ? 5 : this.taskIndex + 1;
    },
    editedOption(value) {
      if (value) {
        setTimeout(() => this.optionInput?.focus(), 10);
      }
    }
  },
  provide() {
    return {
      task: this.task
    };
  }
};
</script>

<style lang="scss">
.task-form {
  border-radius: 1rem;
  box-shadow: $boxshadow2;
  padding: 3.5rem 3rem;
  width: 100%;

  @include respond(mobile) {
    padding: 3rem 2rem;
  }

  &:not(:last-child) {
    margin-bottom: 4rem;

    @include respond(mobile) {
      margin-bottom: 3rem;
    }
  }

  &__top {
    margin-bottom: 3rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  &__title {
    color: $color-blue-2;
    font-size: 2.8rem;

    @include respond(mobile) {
      font-size: 2.2rem;
    }
  }

  &__top-icons {
    transition: all 0.5s;
    display: grid;
    gap: 1.5rem;
    grid-template-columns: repeat(2, min-content);

    @media (hover: hover) {
      opacity: 0;
      visibility: hidden;
    }
  }

  &:hover &__top-icons {
    opacity: 1;
    visibility: visible;
  }

  &__selectors {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    max-width: 55rem;
    margin: auto;
    margin-bottom: 3rem;

    @include respond(mobile) {
      flex-direction: column;
      align-items: center;

      & > *:not(:last-child) {
        margin-bottom: 2rem;
      }
    }
  }

  &__selector-label {
    display: block;
    text-align: center;
    margin-bottom: 1rem;
    font-weight: 600;
    cursor: initial;
  }

  &__option {
    line-height: 1.7;
    display: grid;
    grid-template-columns: 2.8rem 1fr;
    gap: 1.5rem;
    align-items: center;

    @include respond(mobile) {
      line-height: 1.6;
      grid-template-columns: 2.6rem 1fr;
      gap: 1.2rem;
    }

    &:not(:last-child) {
      margin-bottom: 3rem;

      @media (hover: hover) {
        margin-bottom: 2.5rem;
      }
    }
  }

  &__option-icon {
    color: $color-azure;
    font-size: 2.2rem;

    @include respond(mobile) {
      font-size: 1.9rem;
    }
  }

  &__text {
    flex: 1;
    position: relative;

    @include respond(mobile) {
      font-size: 1.45rem;
    }

    span {
      display: block;
    }
  }

  &__text-wrapper {
    span {
      word-wrap: break-word;

      &:not(:last-child) {
        margin-bottom: 0.75rem;
      }
    }
  }

  &__option-actions {
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba($color-azure-light, 0.6);
    border-radius: 0.4rem;
    padding: 0.5rem;
    margin-top: 0.75rem;
    transition: all 0.5s;

    @media (hover: hover) {
      position: absolute;
      width: 10rem;
      height: 100%;
      right: 0;
      top: 50%;
      transform: translateY(-50%);
      padding: 0;
      opacity: 0;
      margin-top: 0;
      visibility: hidden;
    }
  }

  &__text:hover &__option-actions {
    opacity: 1;
    visibility: visible;
  }

  &__option-actions-wrapper {
    display: grid;
    grid-template-columns: repeat(2, min-content);
    justify-content: center;
    gap: 2rem;
  }

  &__option-edit {
    display: block;
    width: 100%;
    font: inherit;
    outline: none;
    padding: 0.8rem;
    border: 0.1rem solid #ccc;
    border-radius: 0.5rem;
    transition: all 0.5s;

    &:focus {
      border-color: $color-azure;
    }
  }

  &__extra {
    font: inherit;
    outline: none;
    width: 100%;
    padding: 0.8rem 2rem;
    border: 0.2rem solid #ccc;
    border-radius: 20px;
    margin-top: 0.5rem;
    line-height: 1.6;
    transition: all 0.5s;

    &:focus {
      border-color: $color-azure;
    }
  }

  a {
    color: $color-blue-2;
    transition: color 0.5s;

    @include respond(tablet-sm) {
      word-break: break-all;
    }

    &:hover {
      color: $color-gold-3;
    }
  }
}

[style="direction: rtl;"] {
  .task-form__option-actions {
    right: initial;
    left: 0;
  }
}
</style>
