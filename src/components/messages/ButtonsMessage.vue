<template>
  <div class="twc-buttons" :class="{ 'twc-expired': replySent || isExpired}">
    <h5 class="twc-buttons-title" v-if="buttonsTitle">{{ buttonsTitle }}</h5>
      <a
        role="button"
        :tabindex="replySent || isExpired ? -1 : 0"
        v-for="(button, idx) in buttonitems"
        :key="idx"
        class="twc-btn"
        :class="{ 'twc-selected': replySent && selected === idx, 'twc-primary': button.style == 'primary', 'twc-secondary': button.style == 'secondary', 'twc-success': button.style == 'success', 'twc-danger': button.style == 'danger', 'twc-warning': button.style == 'warning', 'twc-info': button.style == 'info'}"
        @click="onSelect(button, idx)"
        @keydown="handleReturnSpaceKeys($event, button, idx)"
      >{{ button.title }}</a>
  </div>
</template>

<script>

import handleButtonClick from '../../utils/handle-button-click.js';
import keyIsSpaceOrEnter from '../../utils/is-space-or-enter.js';

export default {
  name: 'ButtonsMessage',
  props: {
    message: {
      type: Object,
      required: true,
      validator: (message) => {
        return (
          message &&
          message.type === 'buttons' &&
          message.data &&
          message.data.button_items &&
          message.data.button_items.length > 0
        );
      },
    },
  },
  computed: {
    buttonsTitle() {
      if (this.message.data.title) {
        return this.message.data.title;
      }
    },
    buttonitems() {
      return this.message.data.button_items;
    },
    replySent() {
      return !!this.message.selected || this.message.selected === 0;
    },
    selected() {
      return this.message.selected;
    },
    isExpired() {
      const { messageList } = this.$teneoApi;
      const latestMessage = messageList[messageList.length - 1];

      return latestMessage && latestMessage !== this.message;
    },
  },
  methods: {
    async onSelect(button, idx) {
      if (this.replySent || this.isExpired) {
        return;
      }
      await handleButtonClick(button, idx, this.$teneoApi)
    },
    handleReturnSpaceKeys(event, reply, idx) {
      if (keyIsSpaceOrEnter(event)) {
        this.onSelect(reply, idx)
      }
    },
  },
};
</script>

<style scoped>
.twc-buttons {
  width: 100%;
  margin: -3px;
  margin-right: 37px;
  text-align: center;
}
</style>

<style>

/*Qiqi - update button color*/
.twc-btn {
  border: 1px solid var(--button-bg-color, #041e42);
  background: var(--button-bg-color, #041e42);
  color: var(--button-fg-color, #ffffff);
  cursor: pointer;
  font-weight: 400;
  text-align: center;
  vertical-align: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  border-radius: 0.25rem;
  min-width: 62px;
  display: inline-block;
  margin: 3px;
  text-decoration: none;
  /* Qiqi -padding: 0.375rem 0.75rem;
  font-size: 0.9em;
  line-height: 1.5;*/
  padding: 4px 16px;
  font-size: 14px;
  line-height: 16px;
  letter-spacing: -0.01em;
  /* Qiqi -update end*/
}

.twc-btn:active {
  outline:none;
}

.twc-expired .twc-btn {
  outline: none;
}

/*Qiqi -update unselected button hover underline hint*/
.twc-btn:hover {
  text-decoration: underline;
}
/*Qiqi -update end*/

/*Qiqi -update button color*/
.twc-btn.twc-selected,
.twc-btn:not(.twc-expired) .twc-btn:hover {
  /*Qiqi - color: var(--button-bg-color, #041e42);
  background: var(--button-fg-color, #ffffff);*/
  color: black;
  background: #CDD1DB;
}
/*Qiqi -update button color*/
.twc-buttons.twc-selected,
.twc-buttons:not(.twc-expired) .twc-btn:hover {
  /*color: var(--button-bg-color, #041e42);
  background: var(--button-fg-color, #ffffff);*/
  color: black;
  background: #CDD1DB;
}

.twc-buttons.twc-expired .twc-btn, .twc-buttons.twc-expired .twc-btn:hover {
  cursor: default;
  /* Qiqi -color: var(--expired-color, #a9a9a9);
  background: var(--button-fg-color, #ffffff);*/
  color: black;
  background: #E7E9EE;
  border: 1px solid var(--expired-color, #a9a9a9);
}

.twc-buttons.twc-expired .twc-btn.twc-selected {
  cursor: default;
  /*Qiqi -color: var(--button-fg-color, #ffffff);
  background: var(--expired-color, #a9a9a9);*/
  color: white ;
  background: #2058A8;
  border: 1px solid var(--expired-color, #a9a9a9);
}

.twc-buttons h5 {
  text-align: center;
  /*Qiqi- change font family*/
  font-family: "Scania Sans Semi Condensed", "Scania Sans Condensed";
  line-height: 1.2;
  margin-top: 0;
  margin-bottom: 0.6rem;
  /*Qiqi- change font size*/
  font-size: 1.8rem;
  font-weight: 500;
  color: var(--buttons-title-color, #263238);
}

.twc-buttons.twc-expired h5 {
  color: var(--expired-color, #a9a9a9);
}

.twc-btn.twc-secondary {
  background: var(--secondary-color, #6c757d);
  border-color: var(--secondary-color, #6c757d);
}

.twc-buttons:not(.twc-expired) .twc-btn.twc-secondary:hover {
  color: var(--secondary-color, #6c757d) !important;
}

.twc-btn.twc-success {
  background: var(--success-color, #28a745);
  border-color: var(--success-color, #28a745);
}

.twc-buttons:not(.twc-expired) .twc-btn.twc-success:hover {
  color: var(--success-color, #28a745) !important;
}

.twc-btn.twc-warning {
  background: var(--warning-color, #ffc107);
  border-color: var(--warning-color, #ffc107);
  color: var(--dark-fg-color, #263238);
}

.twc-buttons:not(.twc-expired) .twc-btn.twc-warning:hover {
  color: var(--warning-color, #ffc107) !important;
}

.twc-btn.twc-danger {
  background: var(--danger-color, #dc3545);
  border-color: var(--danger-color, #dc3545);
}

.twc-buttons:not(.twc-expired) .twc-btn.twc-danger:hover {
  color: var(--danger-color, #dc3545) !important;
}

.twc-btn.twc-info {
  background: var(--info-color, #17a2b8);
  border-color: var(--info-color, #17a2b8);
}

.twc-buttons:not(.twc-expired) .twc-btn.twc-info:hover {
  color: var(--info-color, #17a2b8) !important;
}

</style>
