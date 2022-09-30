<template>
  <div class="floor-panel">
    <h3>{{ floorNumber }}</h3>
    <button :disabled="isActive" @click="addCallOfElevator">Вызов</button>
  </div>
</template>

<script>
export default {
  name: "FloorPanel",
  props: {
    floorNumber: {
      type: Number,
      require: true,
    },
    isActive: {
      type: Boolean,
      default: false,
    },
  },
  methods: {
    addCallOfElevator() {
      this.$emit("addCall", this.floorNumber);
    },
  },
};
</script>

<style scoped>
.floor-panel {
  flex-grow: 1;
}
button {
  cursor: pointer;
}
button:disabled {
  background-color: rgba(245, 109, 109, 0.6);
}
</style>
