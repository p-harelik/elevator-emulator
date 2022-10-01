<template>
  <div class="floor-panel">
    <h3>{{ floorNumber }}</h3>
    <button-call :disabled="isActive" @click="addCallOfElevator" />
  </div>
</template>

<script>
import ButtonCall from "@/components/UI/ButtonCall";
export default {
  name: "FloorPanel",
  components: { ButtonCall },
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
</style>
