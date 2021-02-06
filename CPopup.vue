<template>
  <transition name="popup">
    <div
      v-if="value"
      class="popup"
    >
      <div
        class="popup__overlay"
        @click="$emit('close')"
      />
      <div
        class="popup__container"
        :class="{
          'popup__container--full-screen': fullScreen,
          'popup__container--small': isSmall,
        }"
        :style="{ width }"
      >
        <div
          v-if="$slots.header !== undefined"
          class="popup__header"
        >
          <div
            class="popup__header-text"
            :class="{ 'popup__header-text--small': isSmall }"
          >
            <slot name="header" />
          </div>
          <button
            type="button"
            class="popup__close popup__close--header"
            @click="$emit('close')"
          >
            &times;
          </button>
        </div>
        <button
          v-else
          type="button"
          class="popup__close"
          @click="$emit('close')"
        >
          &times;
        </button>
        <slot name="body" />
        <div
          class="popup__actions"
          :class="{ 'popup__actions--end': actionInEnd }"
        >
          <slot name="actions" />
        </div>
        <div
          class="popup__errors"
          :class="{ 'popup__errors--end': actionInEnd }"
        >
          <slot name="errors" />
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'CPopup',

  props: {
    fullScreen: {
      type: Boolean,
      default: false,
    },

    value: {
      type: Boolean,
      default: false,
    },

    isSmall: {
      type: Boolean,
      default: false,
    },

    actionInEnd: {
      type: Boolean,
      default: true,
    },

    width: {
      type: String,
      default: '600px',
    },
  },

  watch: {
    value(isOpened) {
      document.documentElement.classList.toggle('g-lock', isOpened);
    },
  },
};
</script>

<style lang="scss">
.popup {
  $self: &;

  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 999;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow-x: hidden;
  transition: opacity 0.3s ease;

  &__empty-text {
    margin: 30px;
    font-size: 20px;
    text-align: center;
  }

  &__overlay {
    position: fixed;
    z-index: -1;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(8, 4, 4, 0.8);
  }

  &__header {
    display: flex;
    padding-bottom: 20px;
  }

  &__header-text {
    width: calc(100% - 40px);
    font-size: 24px;
    text-align: left;

    &--small {
      font-size: 20px;
    }
  }

  &__container {
    max-width: 100%;
    max-height: calc(100% - 40px);
    padding: 20px 20px 30px;
    border: 1px solid #555;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.1s ease-out;
    overflow: auto;

    &--full-screen {
      width: 90vw;
      height: 90vh;
    }

    &--small {
      width: 400px;
    }
  }

  &__close {
    cursor: pointer;
    position: absolute;
    top: 0;
    right: 0;
    background: none;
    border: 0;
    outline: none;
    padding: 0 9px;
    color: #91959f;
    font-size: 35px;
    transform: translateZ(0);
    transition: opacity 0.15s ease;

    &:hover {
      opacity: 0.6;
    }

    &--header {
      position: relative;
      top: auto;
      left: auto;
      padding: 0;
      line-height: 1;
      width: 40px;
      text-align: right;
    }
  }

  &__errors,
  &__actions {
    display: flex;
    align-items: center;

    &--end {
      justify-content: flex-end;
    }
  }
}

.popup-enter {
  opacity: 0;
}

.popup-leave-active {
  opacity: 0;
}

.popup-enter .popup-container,
.popup-leave-active .popup-container {
  transform: scale(1.1);
}
</style>
