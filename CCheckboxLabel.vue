<template>
  <span
    class="g-field g-field--no-label c-checkbox-label"
    :class="{
      'c-checkbox-label--disabled': disabled,
      'c-checkbox-label--without-text': $slots.default === undefined,
    }"
  >
    <CCheckbox
      v-model.number="innerValue"
      :disabled="disabled"
      :has-error="hasErrors"
      :true-value="trueValue"
      :false-value="falseValue"
      class="c-checkbox-label__checkbox"
      @input="updateInner"
    />
    <span
      class="c-checkbox-label__text"
      @click="toggleValue"
    >
      <slot />
    </span>
    <Transition name="g-slide-fade-up">
      <span
        v-if="hasErrors"
        class="g-field__error j-error"
      >
        {{ errorMessages[0] }}
      </span>
    </Transition>
  </span>
</template>

<script>
import { isEmpty } from 'chober';

export default {
  name: 'CCheckboxLabel',

  props: {
    value: {
      type: Number,
      required: true,
    },
    errorMessages: {
      type: Array,
      default: () => [],
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    trueValue: {
      type: Number,
      default: 1,
    },
    falseValue: {
      type: Number,
      default: 0,
    },
  },

  data: () => ({
    innerValue: 0,
  }),

  computed: {
    hasErrors() {
      return !isEmpty(this.errorMessages);
    },
  },

  watch: {
    value(value) {
      this.innerValue = value;
    },
  },

  created() {
    this.innerValue = this.value;
  },

  methods: {
    update() {
      this.$emit('input', this.innerValue);
    },

    updateInner(value) {
      this.innerValue = value;
      this.update();
    },

    toggleValue() {
      if (!this.disabled) {
        this.innerValue = this.innerValue === this.trueValue
          ? this.falseValue
          : this.trueValue;
        this.update();
      }
    },
  },
};
</script>

<style lang="scss">
@import "~@global/scss/variables";

.c-checkbox-label {
  $self: &;

  display: flex;
  align-items: center;

  &--without-text {
    padding: 5px 0 0;
  }

  &__text {
    margin-top: 2px;
    margin-left: 8px;
    font: 14px/2.3 $font-main;
    color: $charcoal-grey;
    cursor: default;

    #{$self}--disabled & {
      opacity: 0.5;
    }
  }
}
</style>
