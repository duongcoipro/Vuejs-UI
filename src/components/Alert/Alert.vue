<template>
  <div v-if="visible" :class="`alert alert-${type}`" role="alert">
    <span>{{ message }}</span>
    <button type="button" class="close" @click="hideAlert">
      <span>&times;</span>
    </button>
  </div>
</template>

<script>
export default {
  props: {
    message: {
      type: String,
      required: true,
    },
    type: {
      type: String,
      default: "success", // 'success', 'danger', 'warning', 'info'
    },
  },
  data() {
    return {
      visible: true,
    };
  },
  methods: {
    hideAlert() {
      this.visible = false;
    },
  },
};
</script>

<style scoped>
.alert {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  position: relative;
}

.alert-success {
  color: #3c763d;
  background-color: #dff0d8;
  border-color: #d6e9c6;
}

.alert-danger {
  color: #a94442;
  background-color: #f2dede;
  border-color: #ebccd1;
}

.alert-warning {
  color: #8a6d3b;
  background-color: #fcf8e3;
  border-color: #faebcc;
}

.alert-info {
  color: #31708f;
  background-color: #d9edf7;
  border-color: #bce8f1;
}

.close {
  position: absolute;
  top: 0;
  right: 10px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}
</style>
