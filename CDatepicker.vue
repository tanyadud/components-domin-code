<template>
  <div
    :class="{
      'g-field--no-label': !hasLabel,
      'g-field--loading': loading,
      'c-datepicker--error': hasErrors,
    }"
    class="g-field c-datepicker"
  >
    <span
      v-if="hasLabel"
      :class="{
        'g-field__label--error': hasErrors,
        'g-field__label--success': !hasErrors && hasSuccessMessages,
      }"
      class="g-field__label"
    >
      {{ label }}
    </span>
    <div class="g-field__field-wrapper">
      <div class="g-field__group">
        <button
          :class="{
            'g-field__field--error': hasErrors,
            'g-field__field--success': !hasErrors && hasSuccessMessages,
            'g-field__field--disabled': disabled || loading,
          }"
          :disabled="disabled || loading"
          type="button"
          class="g-field__field g-field__field--button g-field__field--button--left"
          @click="setDay('prev')"
        >
          <CIcon
            name="arrow-left"
            class="c-datepicker__icon-button"
          />
        </button>
        <CIcon
          v-if="!noCalendar"
          name="calendar"
          class="c-datepicker__icon-calendar"
        />
        <Datepicker
          :value="localValue"
          :disabled="disabled || loading || isMonthsPicker || noCalendar"
          :config="localConfig"
          :placeholder="placeholder"
          :class="{
            'g-field__field--error': hasErrors,
            'g-field__field--disabled': disabled || loading,
            'g-field__field--success': !hasErrors && hasSuccessMessages,
            'c-datepicker__field--disabled': disabled || loading || isMonthsPicker || noCalendar,
            'c-datepicker__field--padding': !noCalendar,
          }"
          class="g-field__field c-datepicker__field"
          @input="$emit('input', $event)"
        />
        <button
          :class="{
            'g-field__field--error': hasErrors,
            'g-field__field--success': !hasErrors && hasSuccessMessages,
            'g-field__field--disabled': disabled || loading,
          }"
          :disabled="disabled || loading"
          type="button"
          class="g-field__field g-field__field--button g-field__field--button--right"
          @click="setDay('next')"
        >
          <CIcon
            name="arrow-right"
            class="c-datepicker__icon-button"
          />
        </button>
      </div>
    </div>
    <div
      v-if="hasErrors"
      class="g-field__hint g-field__hint--error j-error"
    >
      {{ errorMessages[0] }}
    </div>
    <div
      v-if="!hasErrors && hasSuccessMessages"
      class="g-field__hint g-field__hint--success-message"
    >
      {{ Array.isArray(successMessages) ? successMessages[0] : successMessages }}
    </div>
    <div
      v-if="hint && !hasErrors && !hasSuccessMessages"
      class="g-field__hint"
    >
      {{ hint }}
    </div>
  </div>
</template>

<script>
// Libs
import { isEmpty } from 'chober';
import moment from 'moment';

// Components
import Datepicker from 'vue-flatpickr-component';
import 'flatpickr/dist/flatpickr.min.css';
import { Russian } from 'flatpickr/dist/l10n/ru';

// Mixins
import UIMixin from '@mixins/UIMixin';

const DEFAULT_CONFIG = {
  altFormat: 'd.m.Y',
  altInput: true,
  dateFormat: 'Y-m-d',
  locale: Russian,
  disableMobile: true,
};

export default {
  name: 'CDatepicker',

  components: {
    Datepicker,
  },

  mixins: [UIMixin],

  props: {
    value: {
      type: String,
      default: '',
    },
    noCalendar: {
      type: Boolean,
      default: false,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    loading: {
      type: Boolean,
      default: false,
    },
    label: {
      type: String,
      default: '',
    },
    placeholder: {
      type: String,
      default: 'Выберите дату',
    },
    name: {
      type: String,
      default: '',
    },
    min: {
      type: String,
      default: '',
    },
    hint: {
      type: String,
      default: null,
    },
    errorMessages: {
      type: Array,
      default: () => [],
    },
    successMessages: {
      type: [Array, String],
      default: '',
    },
    range: {
      type: Number,
      default: null,
    },
    periodType: {
      type: String,
      default: 'day',
    },
  },

  data: () => ({
    localValue: '',
  }),

  computed: {
    localConfig() {
      return {
        ...DEFAULT_CONFIG,
        minDate: this.min,
        altFormat: this.isMonthsPicker ? 'F' : 'd.m.Y',
      };
    },

    hasLabel() {
      return this.label !== '';
    },

    hasErrors() {
      return !isEmpty(this.errorMessages);
    },

    hasSuccessMessages() {
      return !isEmpty(this.successMessages);
    },

    isMonthsPicker() {
      return this.periodType === 'months';
    },
  },

  watch: {
    value() {
      this.setDefaultDay();
    },
  },

  created() {
    this.setDefaultDay();
  },

  methods: {
    setDay(direction) {
      const time = this.isMonthsPicker ? moment(this.localValue).startOf('month') : moment(this.localValue);

      const date = time[direction === 'prev' ? 'subtract' : 'add'](
        this.range === null ? 1 : this.range, this.periodType,
      ).format('Y-MM-DD');

      this.localValue = date;
    },

    setDefaultDay() {
      const time = this.isMonthsPicker ? moment().startOf('month') : moment();
      this.localValue = isEmpty(this.value) ? time.format('Y-MM-DD') : this.value;
    },
  },
};
</script>

<style lang="scss">
@import "~@global/scss/variables";
@import "~@global/scss/field";

.c-datepicker {
  $self: &;

  position: relative;

  &__field {
    text-align: center;
    font-weight: 700;
    width: calc(100% - #{$default-input-height * 2});
    border-radius: 0;

    &--disabled {
      pointer-events: none;
    }

    &--padding {
      padding-left: 45px;
    }

    #{$self}--error & {
      border-color: $light-burgundy;
    }
  }

  &__icon-calendar {
    position: absolute;
    z-index: 4;
    left: 50px;
    top: 7px;
    font-size: 18px;
    color: $true-green;
    pointer-events: none;
  }

  &__icon-button {
    font-size: 14px;
  }
}

.flatpickr-calendar {
  box-shadow: 0 2px 10px 0 rgba(0, 0, 0, 0.24);
  border: 0;
  border-radius: 3px;
  width: 290px;
  padding: 10px 16px 16px;

  &::before,
  &::after {
    display: none;
  }
}

.flatpickr-months .flatpickr-prev-month.flatpickr-prev-month,
.flatpickr-months .flatpickr-next-month.flatpickr-prev-month {
  left: 15px;
  top: 10px;
}

.flatpickr-months .flatpickr-prev-month.flatpickr-next-month,
.flatpickr-months .flatpickr-next-month.flatpickr-next-month {
  right: 15px;
  top: 10px;
}

.flatpickr-day {
  width: 32px;
  height: 28px;
  line-height: 28px;
  border-radius: 3px;
  color: $charcoal-grey;

  &.selected,
  &.startRange,
  &.endRange,
  &.selected.inRange,
  &.startRange.inRange,
  &.endRange.inRange,
  &.selected.prevMonthDay,
  &.startRange.prevMonthDay,
  &.endRange.prevMonthDay,
  &.selected.nextMonthDay,
  &.startRange.nextMonthDay,
  &.endRange.nextMonthDay,
  &.selected:focus,
  &.startRange:focus,
  &.endRange:focus,
  &.selected:hover,
  &.startRange:hover,
  &.endRange:hover {
    background: $true-green;
    border-color: $true-green;
    transition: all 0.15s ease;
    transition-property: background-color, border-color, color;
  }

  &:hover,
  &:focus,
  &.inRange,
  &.prevMonthDay.inRange,
  &.nextMonthDay.inRange,
  &.today.inRange,
  &.prevMonthDay.today.inRange,
  &.nextMonthDay.today.inRange,
  &.prevMonthDay:hover,
  &.nextMonthDay:hover,
  &.prevMonthDay:focus,
  &.nextMonthDay:focus {
    background: rgba($slime-green, 0.26);
    border-color: transparent;
    color: $charcoal-grey;
  }

  &.disabled {
    &,
    &:hover {
      background: none;
      color: rgba(#393939, 0.1);
    }
  }
}

.flatpickr-days {
  width: auto;
}

.flatpickr-months,
.flatpickr-month {
  height: 40px;
}

.flatpickr-current-month {
  font-size: 110%;
}

.flatpickr-current-month input.cur-year {
  font-weight: 700;
}

span.flatpickr-weekday {
  text-transform: lowercase;
  color: rgba($charcoal-grey, 0.56);
}

.dayContainer {
  width: 256px;
  min-width: 256px;
  max-width: 256px;
}
</style>
