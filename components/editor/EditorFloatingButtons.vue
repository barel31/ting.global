<template>
  <FloatingButtons>
    <ActionButton type="info" color="white" @click="openIntroModal" />
    <ActionButton
      v-if="!templateOnlyMode"
      type="shuffle"
      color="white"
      @click="selectRandomOptions"
    />
  </FloatingButtons>
</template>

<script>
export default {
  inject: ["templateOnlyMode", "data", "openIntroModal", "setConfirmModal"],
  methods: {
    selectRandomOptions() {
      this.setConfirmModal(
        "Do you want to select a random option for each task? All your selections would be overwritten.",
        () => {
          this.data.days.forEach(day => {
            day.tasks.forEach(task => {
              const optionIndex = Math.floor(
                Math.random() * task.options.length
              );
              task.selection = task.options[optionIndex]?.text;
            });
          });
        }
      );
    }
  }
};
</script>

<style lang="scss">
.editor {
  .floating-buttons {
    & > .action-button {
      outline: none;
    }
  }
}
</style>
