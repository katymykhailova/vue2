<template>
  <transition name="modal">
    <div v-if="isOpen" class="modal-backdrop" @click="close">
      <div class="modal-content" @click.stop>
        <div class="modal-title-bar">
          <h2 class="modal-title">{{ title }}</h2>
          <button @click.stop="close" class="modal-close-btn">
            <svg
              width="30"
              height="30"
              viewBox="0 0 30 30"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path d="M8 8L22 22" stroke="white" stroke-width="2" />
              <path d="M8 22L22 8" stroke="white" stroke-width="2" />
            </svg>
          </button>
        </div>

        <slot></slot>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'Modal',
  data() {
    return { isOpen: false };
  },

  props: {
    title: { type: String, default: '' },
  },

  methods: {
    open() {
      document.querySelector('body').style.overflow = 'hidden';
      this.isOpen = true;
    },

    close() {
      document.querySelector('body').style.overflow = 'auto';
      this.isOpen = false;
    },
  },
};
</script>

<style>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1200;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding-top: 10px;
  background-color: black;
}

.modal-title-bar {
  position: relative;
}

.modal-close-btn {
  position: absolute;
  top: 0;
  right: 5px;
  margin: 0;
  padding: 0;
  background: transparent;
  border: 0;
  border-radius: 3px;
  transition: transform 300ms cubic-bezier(0.4, 0, 0.2, 1);
}
.modal-close-btn:hover {
  transform: scale(1.3);
}

.modal-title {
  font-size: 18px;
  padding: 0 10px;
  margin: 0 0 10px 0;
  color: #ffffff;
}

.modal-enter-active {
  transition: all 300ms cubic-bezier(0.4, 0, 0.2, 1);
}

.modal-leave-active {
  transition: all 300ms cubic-bezier(0.4, 0, 0.2, 1);
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}
</style>
