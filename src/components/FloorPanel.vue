<template>
  <div class="floor-panel">
    <h3>{{ floorNumber }}</h3>
    <button>Вызов</button>
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
</style>
